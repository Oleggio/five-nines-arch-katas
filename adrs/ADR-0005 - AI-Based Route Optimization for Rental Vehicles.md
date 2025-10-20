# ADR-0005 — AI-Based Route Optimization for Rental Vehicles

## Context
Customers using our vehicle rental app require **personalized route recommendations** that adapt to changing conditions such as **weather, traffic, events, and user preferences** (e.g., scenic vs. fast routes). Traditional route calculation APIs (e.g., Google Maps, Here, TomTom) provide static route options optimized primarily for time or distance. However, our business objective is to **differentiate through adaptive, GenAI-driven route personalization**, enhancing customer satisfaction, retention, and premium service uptake.

The system must support:
- Real-time environmental data ingestion (traffic, weather, events, regulation restrictions).  
- Fixed environmental data ingestion (license & rental territory limitations).  
- Multi-objective route optimization (speed, comfort, sightseeing, eco-efficiency).  
- Integration with device sensors (battery charging level).  
- Integration with pricing systems (for cost optimization and riding-terms control).  
- Route guiding and sightseeing information (optional voice guidance).  

## Decision
Implement a **cloud-based AI route optimization engine** powered by **Generative AI** and governed by a **Model Control Plane (MCP)** for centralized management and deployment.  
The engine dynamically calculates routes using real-time data and learned user preferences.  
Core design elements include:
- **Predictive ML models** for travel time and satisfaction scoring.  
- **Rule-based constraints** for compliance (speed limits, vehicle range, regulations).  
- **Reinforcement Learning (RL)** for continuous route preference optimization from feedback (ratings, route choices, completion times).  
- **GenAI summarization** to generate contextual route insights and explanations for users.  

All model training, inference, and telemetry will run in the **cloud environment**, ensuring scalability, observability, and controlled rollout.

The engine will expose APIs to:
- Recommend top N routes under current conditions.  
- Re-rank routes when external factors (traffic, weather, events) change.  
- Log route outcomes to retrain ML and RL models periodically.  

## Key Differentiators
- **Personalization**  
  Uses contextual and behavioral data to tailor the route experience — e.g., “fastest under rain” vs. “most scenic on weekends.”

- **Weather-Aware Routing**  
  Adjusts route recommendations dynamically based on precipitation, temperature, and visibility thresholds. Special routing for rain/wind conditions.

- **Experience Mode**  
  Allows users to select between *Fast*, *Balanced*, or *Sightseeing* modes, influencing model weighting parameters.

- **Event & Traffic Intelligence**  
  Integrates with city APIs and traffic sensors to avoid congested or restricted zones due to concerts, parades, or repairs.

- **Cloud Centralization**  
  Ensures consistent model deployment, telemetry collection, and performance monitoring via the MCP.

- **Expandability**  
  Modular architecture allows adding new context factors (e.g., air quality, road conditions, tolls) or new geographies with minimal retraining.

## Alternatives Considered
- **Alternative 1: Static API-Based Routing**  
  Use third-party route APIs (e.g., Google Directions) with fixed parameters.  
  *Rejected* — lacks personalization and contextual adaptivity; would not differentiate the user experience.

- **Alternative 2: Rules-Only System (No ML)**  
  Predefined rules for weather and traffic conditions.  
  *Rejected* — does not scale with user diversity or continuous learning needs.

- **Alternative 3: Distributed Edge Processing**  
  Process data at local nodes for low-latency optimization.  
  *Rejected* — adds infrastructure complexity and high maintenance cost; latency can be tolerated within cloud-based processing limits.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|-----------|-------------|-------------|
| **Latency** | Cloud inference may add small delay for real-time recalculations. | Optimize request batching, use CDN and regional compute zones. |
| **Scalability** | Sudden spikes in usage may impact response time. | Auto-scaling of compute clusters and caching of frequent routes. |
| **Data Consistency** | Model versions and datasets must remain synchronized across environments. | Governed by MCP for version control and rollback automation. |
| **Operational Overhead** | Centralized cloud processing requires monitoring and continuous observability. | Integrate with cloud monitoring stack (Prometheus, Grafana, CloudWatch). |
| **Security & Privacy** | GPS and PII data must be protected in transit and at rest. | Tokenization, encryption, strict IAM, and GDPR-aligned data policies. |
| **Model Accuracy** | Models may degrade if environment changes rapidly. | Continuous retraining pipeline with drift detection. |
| **Cost vs Differentiation** | GenAI and cloud compute add cost overhead. | Prioritize premium route experience to justify value. |

> **Note:**  
> - All model deployment, telemetry, and rollback logic will be orchestrated by the **cloud-native MCP**.  
> - No edge hardware or local inference is required.  
> - The system will rely on regional cloud nodes and intelligent caching for responsiveness.

## Conclusion
We will deploy a **hybrid AI route optimization service** leveraging ML for personalization, RL for continuous improvement, and rule-based logic for safety and compliance. The system will initially integrate with Google Maps for baseline route data and progressively transition to in-house predictive and generative models.  

To ensure governance, traceability, and safe rollout of models, we will apply a **cloud-based Model Control Plane (MCP)** — managing model versions, deployment policies, telemetry, and automated rollback in case of degradation. This provides full control of model lifecycles within the cloud environment, ensuring reliability and compliance during continuous updates.  

The design supports modular extension for future contexts such as energy consumption, CO₂ optimization, and conversational GenAI-driven user assistance.
