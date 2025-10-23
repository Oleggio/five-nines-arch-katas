# ADR-0022 — Demand Forecasting Model Selection and Architecture

## Context

Demand forecasting is critical for proactive vehicle positioning and fleet utilization optimization across **all vehicle types** (scooters, e-bikes, cars, vans), addressing the business challenge "forecast demand to improve vehicle positioning" (FR#2G, FR#2H). The system must predict future rental demand by location, time, and vehicle type to enable crew repositioning before peak hours, reducing "vehicle not available" scenarios and improving utilization rates toward the 70% KPI target.

**Vehicle-Type-Specific Demand Patterns:**
- **Scooters/E-bikes**: Highly weather-sensitive; short trips (1-5km); hourly volatility; peak during commute hours; low demand in rain/cold
- **Cars/Vans**: Less weather-sensitive; longer trips (10-50km); advance bookings (up to 7 days); daily/weekly patterns; consistent business/leisure demand

This ADR defines the model architecture and selection process for multi-modal fleet demand forecasting, building on established decisions:
- ADR-0003: Vertex AI as core platform for AI/ML workloads
- ADR-0006: Knowledge Management on GCP (BigQuery, Cloud Storage, Dataflow for historical data)
- ADR-0009: Model lifecycle management using Vertex AI Model Registry
- ADR-0011: Model evaluation gates and continuous monitoring
- ADR-0002: Vehicle telemetry & integration stack (event publishing via Pub/Sub)

Business requirements and constraints:
- FR#2H: Predict hourly demand by location zone and vehicle type; 24-hour forecast horizon; weekly retraining
- NFR-AI-010: Accuracy targets:
  - Short-term (4h): ≥80% within ±20%
  - Medium-term (24h): ≥75% within ±25%
  - Long-term (7d): ≥65% within ±30%
- NFR-AI-002: Forecast generation ≤60 seconds for 24h ahead; batch processing ≤10 minutes for 7-day rolling forecasts

Industry best practices and standards:
- Mean Absolute Percentage Error (MAPE) as standard time-series accuracy metric
- Root Mean Squared Error (RMSE) for error magnitude assessment
- Forecast reconciliation for hierarchical predictions (city → zone → vehicle type)
- Backtesting with walk-forward validation for temporal data integrity
- Exogenous variables integration (weather, events, holidays)

## Decision

Adopt a **staged approach** with clear upgrade path, prioritizing rapid value delivery and incremental accuracy improvement:

### Phase 1: Statistical Time-Series Models (Initial Production)
- **Start with proven statistical methods** for rapid deployment and interpretability
- Primary model: **Prophet** (Facebook's open-source forecasting library)
  - Handles seasonality (hourly, daily, weekly patterns)
  - Robust to missing data and outliers
  - Interpretable trend and seasonality components
  - Built-in handling of holidays and special events
- **Vehicle-type-specific model configurations**:
  - **Scooters/E-bikes**: Hourly seasonality (strong commute peaks); daily regressors for weather (rain reduces demand 40-60%); shorter training window (30 days)
  - **Cars/Vans**: Daily + weekly seasonality (weekend/weekday patterns); weekly regressors for events; longer training window (90 days)
  - **Deployment**: Separate Prophet instances per vehicle type to capture distinct patterns, sharing infrastructure
- Fallback model: **ARIMA/SARIMA** for stationary demand patterns
- Deploy on Vertex AI Workbench for training; serve predictions via Cloud Run or BigQuery scheduled queries
- Target metrics:
  - 24h MAPE ≤25% (threshold for Phase 2 escalation: >30% for 4 consecutive weeks)
  - Forecast generation time ≤60s per zone per vehicle type
  - Retraining cadence: Weekly for cars/vans (90-day window), 2x weekly for scooters/e-bikes (30-day window due to higher volatility)

### Phase 2: ML-Based Forecasting (If Statistical Models Plateau)
- **Escalate to gradient-boosting or neural network models** if:
  - Prophet/ARIMA MAPE consistently >30% for 24h forecasts (for any vehicle type)
  - Complex non-linear patterns detected (event-driven spikes, competitor impacts)
  - Business feedback indicates missed repositioning opportunities
- Candidate architectures:
  - **LightGBM or XGBoost** (gradient-boosting): excellent for tabular data with exogenous features
  - **LSTM/GRU** (recurrent neural networks): capture long-term temporal dependencies
  - **Temporal Fusion Transformer (TFT)**: state-of-the-art for multi-horizon forecasting with interpretability
- **Vehicle-type modeling strategy**:
  - **Option A (Recommended)**: Separate models per vehicle type for specialized pattern learning
    - Pros: Capture unique seasonality (e.g., scooter weather sensitivity); easier to debug; independent deployment
    - Cons: More models to maintain; no cross-vehicle learning
  - **Option B**: Single multi-output model with vehicle_type as categorical feature
    - Pros: Shared learning across vehicle types; fewer models; consistent predictions
    - Cons: May dilute vehicle-specific patterns; harder to tune
  - **Decision**: Start with Option A; consider Option B if cross-vehicle patterns emerge (e.g., citywide events affect all types similarly)
- Training approach:
  - Feature engineering: lag features, rolling statistics, time embeddings, **vehicle-type-specific weather impact** (rain coefficient differs: scooters -60%, cars -10%)
  - Hyperparameter tuning via Vertex AI Hyperparameter Tuning
  - Ensemble methods: combine multiple models for robustness
- Deployment:
  - Vertex AI Prediction endpoints with autoscaling
  - A/B testing against Prophet baseline (champion/challenger) **per vehicle type**

### Data Pipeline and Feature Engineering
- **Historical booking data** (source: Pub/Sub events → BigQuery via Dataflow):
  - `rental_started`, `rental_ended`, `booking_created`, `booking_cancelled`
  - Aggregated to **vehicle-type-specific granularity**: hourly for scooters/e-bikes, hourly/daily for cars/vans
  - Retention: 2 years for training, 90 days for real-time retraining (scooters: 30 days due to faster retraining)
- **Exogenous features** (enhance forecast accuracy, **vehicle-type-weighted**):
  - **Weather data** (temperature, precipitation, wind) via external API (OpenWeather, Weather Company)
    - **Scooters/E-bikes**: High impact — rain reduces demand 40-60%, temp <5°C reduces 30%, wind >25km/h reduces 20%
    - **Cars/Vans**: Moderate impact — rain increases demand 10-15% (alternative to bikes), temp minimal effect
  - Local events calendar (concerts, sports, conferences) → demand spikes **across all vehicle types** (amplitude varies)
  - Holiday calendar (public holidays, school breaks) → usage pattern shifts (scooters drop, cars increase)
  - Historical promotions/pricing changes → elasticity effects (scooters more price-sensitive than cars)
- **Vehicle-type-specific feature engineering**:
  - **Scooters/E-bikes**: Trip distance <5km filter; exclude winter months from baseline in cold climates; weekend vs weekday commute patterns
  - **Cars/Vans**: Advance booking lead time (1-7 days); weekend leisure spikes; airport proximity features
- **Feature store** (Vertex AI Feature Store):
  - Centralized storage for engineered features (shared across vehicle types)
  - Online serving for real-time inference
  - Offline serving for batch training
- **Data quality checks**:
  - Missing value imputation (forward fill for short gaps, drop for long gaps)
  - Outlier detection (remove anomalies >3σ from historical mean, **per vehicle type**)
  - Schema validation (Great Expectations or similar)

### Model Training and Evaluation (ADR-0011)
- **Training cadence**: Weekly batch retraining on rolling 90-day window
- **Validation strategy**: Walk-forward validation (time-series cross-validation)
  - Train on months 1-2, validate on month 3
  - Roll forward weekly to prevent data leakage
- **Evaluation metrics**:
  - Primary: MAPE (Mean Absolute Percentage Error)
  - Secondary: RMSE (Root Mean Squared Error), MAE (Mean Absolute Error)
  - Directional accuracy (did we predict increase/decrease correctly?)
- **Deployment gates** (ADR-0011):
  - New model must outperform champion by ≥5% MAPE reduction
  - Backtesting on last 4 weeks must meet NFR-AI-010 thresholds
  - Shadow deployment for 1 week before full promotion
- **Continuous monitoring**:
  - Track forecast vs actual demand daily
  - Alert if MAPE degrades >10% from baseline over 7-day window
  - Drift detection on input features (distribution shifts)

### Prediction Output Format
All models must output standardized JSON for consumption by crew mobile app (FR-CV-027) and operations dashboards, **supporting all vehicle types**:
```json
{
  "forecast_timestamp": "2025-10-22T10:00:00Z",
  "horizon_hours": 24,
  "location_zone": "financial_district_london",
  "predictions": [
    {
      "hour": "2025-10-23T08:00:00Z",
      "vehicle_type": "car",
      "predicted_demand": 12,
      "confidence_interval_lower": 9,
      "confidence_interval_upper": 15,
      "model_version": "prophet-v2.3.1"
    },
    {
      "hour": "2025-10-23T08:00:00Z",
      "vehicle_type": "scooter",
      "predicted_demand": 45,
      "confidence_interval_lower": 35,
      "confidence_interval_upper": 55,
      "model_version": "prophet-scooter-v1.2.0",
      "weather_impact": "rain_forecast_reduces_demand_30pct"
    }
  ],
  "repositioning_suggestions": [
    {
      "action": "move_from",
      "source_zone": "suburban_north",
      "target_zone": "financial_district_london",
      "vehicle_type": "car",
      "vehicle_count": 3,
      "priority": "high",
      "reason": "predicted_demand_spike_8am"
    },
    {
      "action": "battery_swap",
      "zone": "financial_district_london",
      "vehicle_type": "scooter",
      "vehicle_count": 15,
      "priority": "medium",
      "reason": "high_demand_forecast_requires_charged_fleet"
    }
  ]
}
```

### Integration with Operations Workflow
- **Crew repositioning tasks** (FR-CV-027, **all vehicle types**):
  - Forecasting system publishes high-priority repositioning suggestions for each vehicle type
  - Crew mobile app displays tasks sorted by urgency, proximity, and vehicle type specialization
  - **Examples**:
    - **Scooters**: Move 20 scooters from suburban_west to university_campus before 8am (predicted commute surge)
    - **E-bikes**: Battery swap 15 e-bikes in city_center before lunch rush (predicted demand + low charge)
    - **Cars**: Reposition 5 cars from residential_area to airport before weekend (business travel forecast)
  - Crew executes relocation; system tracks completion
- **Post-analysis feedback loop**:
  - Compare forecast vs actual demand hourly **per vehicle type**
  - Flag large deviations (>40%) for root cause analysis (e.g., unexpected rain for scooters, event cancellation for cars)
  - Use misses to augment training data and retrain
- **Business dashboards**:
  - Operations team views 7-day rolling forecasts **segmented by vehicle type**
  - Heatmaps showing predicted demand hotspots (color-coded by vehicle type)
  - Repositioning efficiency metrics (forecast-driven moves vs ad-hoc), **stratified by scooters/bikes vs cars/vans**

### Compliance and Data Governance
- **GDPR compliance** (ADR-0001):
  - Aggregated demand data (no PII in forecasting models)
  - Customer IDs pseudonymized if used for segmentation features
- **Data retention** (ADR-0006):
  - Raw events: 90 days in Pub/Sub, 2 years in BigQuery
  - Aggregated demand timeseries: indefinite (business intelligence asset)
- **Model artifacts**:
  - Versioned in Vertex AI Model Registry (ADR-0009)
  - Training datasets catalogued with lineage in Data Catalog

## Key Differentiators

- **Staged Pragmatism**  
  Start with interpretable statistical models (Prophet) for rapid delivery; escalate to ML only if business value justifies complexity.

- **Multi-Modal Fleet Awareness**  
  Separate model configurations per vehicle type (scooters/e-bikes vs cars/vans) to capture distinct demand patterns, weather sensitivity, and seasonality.

- **Vehicle-Type-Specific Feature Engineering**  
  Weather impact coefficients differ by vehicle type: rain reduces scooter demand 40-60% but increases car demand 10-15%; exogenous features weighted accordingly.

- **Exogenous Feature Integration**  
  Weather, events, holidays significantly improve forecast accuracy vs naive time-series models; structured via Feature Store.

- **Hierarchical Forecasting**  
  Predict at multiple granularities (city → zone → vehicle type) and reconcile for consistency.

- **Operational Integration**  
  Forecasts directly drive crew repositioning tasks (scooter relocation, battery swaps, car repositioning); feedback loop improves model over time.

- **Cost Efficiency**  
  Batch predictions for 7-day forecasts; real-time only for urgent needs; leverage BigQuery scheduled queries for Prophet; shared infrastructure across vehicle types.

## Alternatives Considered

- **Prophet (Selected for Phase 1)**  
  Pros: Fast to deploy; handles seasonality/holidays well; interpretable; open-source.  
  Cons: Limited for complex non-linear patterns; assumes additive seasonality (can switch to multiplicative).

- **ARIMA/SARIMA**  
  Pros: Classic time-series method; well-understood; good for stationary data.  
  Cons: Requires manual parameter tuning (p, d, q); poor handling of exogenous variables; fragile with missing data.  
  Decision: Use as fallback only for specific zones with stationary demand.

- **Gradient-Boosting ML (LightGBM/XGBoost)**  
  Pros: Excellent accuracy with engineered features; handles non-linearity; fast training.  
  Cons: Requires feature engineering expertise; less interpretable; needs more data.  
  Decision: Phase 2 escalation if Prophet underperforms.

- **LSTM/GRU (Recurrent Neural Networks)**  
  Pros: Captures long-term dependencies; end-to-end learning.  
  Cons: Requires large datasets (>1 year); slow training; harder to debug; overfitting risk.  
  Decision: Consider only if booking history >2 years and clear sequential patterns exist.

- **Temporal Fusion Transformer (TFT)**  
  Pros: State-of-the-art multi-horizon forecasting; built-in attention for interpretability.  
  Cons: Complex architecture; requires significant compute; longer development time.  
  Decision: Future consideration if demand forecasting becomes core business differentiator.

- **Third-Party Forecasting APIs (e.g., AWS Forecast, Azure AutoML for Time Series)**  
  Pros: Fully managed; AutoML for model selection; minimal ML expertise required.  
  Cons: Vendor lock-in; data egress costs; limited customization; conflicts with ADR-0003 (Vertex AI platform).  
  Decision: Rejected in favor of Vertex AI native approach.

- **On-Premise Statistical Software (R, SAS)**  
  Pros: Full control; established enterprise tooling.  
  Cons: Infrastructure overhead; manual deployment pipelines; doesn't leverage cloud scalability.  
  Decision: Rejected; conflicts with cloud-native strategy.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|-----------|-------------|------------|
| Accuracy shortfall | Prophet may not capture complex event-driven demand spikes (especially for scooters in variable weather) | Integrate events calendar; escalate to ML if MAPE >30%; confidence intervals guide crew decisions; vehicle-type-specific thresholds |
| Data quality | Missing or incorrect booking events degrade forecasts | Schema validation; anomaly detection; human-in-the-loop review for outliers; per-vehicle-type quality metrics |
| Cold start | Insufficient historical data for new cities/vehicle types | Transfer learning from similar zones; use simple baseline (moving average) until 3 months data available; borrow scooter patterns from existing cities |
| Vehicle-type imbalance | Scooters (5,000/country) vs cars (200/country): different data volumes and volatility | Separate retraining schedules: scooters 2x/week (30-day window), cars weekly (90-day window); independent accuracy targets |
| Weather dependency | Scooters highly weather-sensitive; API outages or forecast errors cascade to demand forecasts | Cache historical weather patterns as fallback; use ensemble with weather-free baseline; alert crew when weather impact >30% |
| Exogenous dependency | Weather API outages or event data gaps reduce accuracy | Graceful degradation to time-series-only model; cache historical weather patterns as fallback |
| Latency | Forecast generation >60s for complex zones with multiple vehicle types | Pre-compute forecasts hourly per vehicle type; cache predictions; use simpler model for on-demand requests |
| Overfitting | ML models memorize noise instead of signal (risk higher for low-volume cars/vans) | Walk-forward validation; regularization; ensemble methods; early stopping; monitor per-vehicle-type |
| Drift | Seasonal patterns shift (new competitors, pricing changes, new scooter/bike models) | Continuous monitoring (ADR-0011); quarterly model retraining; A/B testing for new versions; track per-vehicle-type drift |
| Interpretability | ML models harder to explain to operations teams | SHAP values for feature importance; confidence intervals; compare to Prophet baseline; vehicle-type-specific dashboards |
| Cost | High BigQuery/Vertex AI usage for frequent retraining (4 vehicle types × many zones) | Optimize queries; use scheduled jobs vs on-demand; cache aggregated features; monitor budgets; shared infrastructure where possible |

## Conclusion

Adopt a staged approach for **multi-modal fleet demand forecasting** (scooters, e-bikes, cars, vans): begin with Prophet (statistical time-series model) with **vehicle-type-specific configurations** for rapid deployment and interpretability, deployed on Vertex AI Workbench with predictions served via BigQuery or Cloud Run. Integrate **vehicle-type-weighted exogenous features** (weather impact: scooters -60%, cars +10%; events, holidays) via Vertex AI Feature Store. Use **separate model instances per vehicle type** to capture distinct seasonality patterns (scooters: hourly volatility + weather sensitivity; cars: daily/weekly patterns + advance bookings). If accuracy targets (NFR-AI-010) are not met for any vehicle type, escalate to gradient-boosting ML (LightGBM/XGBoost) or neural networks (LSTM/TFT) hosted on Vertex AI Prediction. All models must integrate with evaluation gates (ADR-0011), continuous monitoring, and **vehicle-type-specific crew repositioning workflows** (scooter relocation, battery swaps, car repositioning per FR-CV-027). This approach balances speed-to-market, operational simplicity, vehicle-specific accuracy, and long-term improvement across the entire fleet.
