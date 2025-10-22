# ADR-0019 - Hybrid AI for Maintenance Optimization: Supply Efficiency

## Context

The rental platform operates a large and geographically distributed fleet of vehicles (scooters, cars, vans, e-bikes).  
Operational efficiency depends on optimizing **vehicle supply**, **charging**, and **maintenance** tasks executed by the field (Ops) team — particularly during off-peak hours.

Functional Requirements [FR#2H](/requirements/2_FRs.md) and [FR#2I](/requirements/2_FRs.md) define:
> “Calculate supply (vehicle movement) route and timing, calculate charging tasks for the operational team in the night.”  
> “Calculate recommended load distribution (even rental/usage among vehicles), then suggest a vehicle to book from the location.”

These scenarios aim to:
- evenly distribute wear across the fleet (reduce overuse of certain vehicles)
- optimize nightly relocation and charging routes for the Ops team
- reduce idle time and maintenance cost through predictive insights
- integrate with demand forecasting ([FR#2G](/requirements/2_FRs.md)) for pre-emptive supply positioning

Traditional rule-based scheduling or manual planning is inefficient when managing hundreds of vehicles across multiple districts.  
A **Hybrid AI** approach (ML + GenAI + rule-based logic) ensures continuous optimization, explainability, and compliance with logistical and safety constraints.

## Decision

Adopt a **Hybrid AI for Maintenance Optimization** that combines:
- **ML Models** for:
  - Predicting supply imbalance and relocation needs per area and time window.  
  - Estimating charging and maintenance demand using telemetry and usage history.
  - Estimating even
- **GenAI Layer** for:
  - Generating routes with multiple stops, and Ops team instructions per each transporting/charging/maintenance task.  
  - Considering real-time parameters for routing (weather, traffic jam and other changing map parameters).
- **Rule-Based Logic** for:
  - Hard constraints (vehicle type capacity, parking lots capacity, team shift limits, safety regulations, license limitations).  

This combination yields an automated, interpretable optimization cycle that runs nightly and provides the Ops dashboard with ready-to-execute plans and dynamic updates when conditions change (e.g., battery failure, weather).

## Key Differentiators

- **Consideration_1 — Continuous Fleet-Wide Optimization**  
  The system continuously monitors telemetry, bookings, and predicted demand to rebalance supply and assign Ops tasks dynamically.

- **Consideration_2 — Multi-Objective Balancing (Cost / Distance / Load)**  
  Optimization considers multiple KPIs — route length, time window, load balance, and total energy consumption — instead of a single static metric. It also considers real-time conditions like weather.

- **Consideration_3 — Integration with Maintenance Forecasting (FR#2J)**  
  The same data feeds maintenance-timing predictions and enables pre-emptive scheduling. The system output is used to create tasks for ops team. 

## Alternatives Considered

- **Alternative_1: Rule-Based Planning Only**  
  Simple to implement but scales poorly and ignores contextual factors like weather, events, or dynamic demand as well as other ops load.
- **Alternative_2: ML-Only Optimization**  
  High performance for prediction but produces non-explainable and sometimes impractical task plans for human operators. No real-time input for weather and map-based routing.
- **Alternative_3: Human Manual Scheduling**  
  High operational cost, prone to errors, and unscalable beyond small fleet sizes.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|--|--|--|
| **Model Drift** | Demand and supply patterns shift seasonally, reducing prediction accuracy | Periodic retraining and validation using recent telemetry and demand logs |
| **Operational Overload** | Generated tasks may exceed Ops team capacity | Enforce rule-based caps per shift and vehicle category, consider ops team capacity and remaining tasks |
| **Latency / Scalability** | Recomputing supply routes for large fleets can be resource-intensive | Schedule incremental recalculations; leverage Vertex AI Pipelines with parallel task queues |
| **Data Quality** | Incorrect telemetry and false parametrizing can degrade optimization accuracy | Implement validation and fallback logic to use historical averages |
| **Cost** | Too frequent optimization as well as overengineered calculation may increase costs | Use threshold triggers; apply optimal number of parameters |

## Conclusion
The Hybrid AI architecture for Maintenance Optimization creates an **intelligent and cost-efficient** supply-management loop. By merging ML demand predictions, rule-based constraints, and GenAI-driven task calculation based on real-time routing, weather and team load parameters, the platform achieves perfect vehicle availability, balanced utilization, and reduced maintenance overhead — ensuring smooth operations and optimal fleet health across all locations.
