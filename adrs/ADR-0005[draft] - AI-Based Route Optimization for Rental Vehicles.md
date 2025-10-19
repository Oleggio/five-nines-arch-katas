 # ADR-0005 - AI-Based Route Optimization for Rental Vehicles
## Context
Customers using our vehicle rental app require **personalized route recommendations** that adapt to changing conditions such as **weather, traffic, events, and user preferences** (e.g., scenic vs. fast routes). Traditional route calculation APIs (e.g., Google Maps, Here, TomTom) provide static route options optimized primarily for time or distance. However, our business objective is to **differentiate through adaptive, AI-driven route personalization**, enhancing customer satisfaction, retention, and premium service uptake.

The system must support:
- Real-time environmental data ingestion (traffic, weather, events, regulation restrictions).
- Fixed environmental data ingestion (license & rental territory limitations).
- Multi-objective route optimization (speed, comfort, sightseeing, eco-efficiency).
- Integration with device sensors (battery charging level).
- Integration with pricing systems (for cost optimization and riding terms control).
- Edge computing for route optimizing (low-latency decisioning in urban mobility zones).
- Route guiding / info about sightseeings passed (voice guide while driving if chosen by user).

 ## Decision
Implement an **AI-based route optimization engine** that dynamically calculates routes using real-time data and learned user preferences. The core will be a **hybrid model** combining:
- **Predictive ML models** for travel time and satisfaction scoring.
- **Rule-based constraints** for compliance (speed limits, vehicle range, regulations).
- **Reinforcement Learning (RL)** for continuous route preference optimization based on feedback loops (ratings, route choices, completion times).

The engine will expose APIs to:
- Recommend top N routes under current conditions.
- Re-rank routes when external factors (traffic, weather, events) change.
- Log route outcomes to retrain ML and RL models periodically.

## Key Differentiators

- **Personalization**  
  Uses contextual and behavioral data to tailor the route experience — e.g., "fastest under rain" vs. "most scenic on weekends."

- **Weather-Aware Routing**  
  Adjusts route recommendations dynamically based on precipitation, temperature, and visibility thresholds. Special routes during rain/wind.

- **Experience Mode**  
  Allows users to select between *Fast*, *Balanced*, or *Sightseeing* modes, influencing AI weighting parameters.

- **Event & Traffic Intelligence**  
  Integrates with city APIs and traffic sensors to avoid congested or restricted zones due to concerts, parades, or repairs.

- **Real-Time Adaptivity**  
  Recomputes optimal routes mid-trip if conditions shift significantly, ensuring reliability without manual re-navigation.

- **Expandability**  
  Modular architecture allows adding new context factors (e.g., air quality, road conditions, tolls) or new geographies with minimal retraining.

## Alternatives Considered

- **Alternative 1: Static API-Based Routing**  
  Use third-party route APIs (e.g., Google Directions) with fixed parameters.  
  *Rejected* — lacks personalization and contextual adaptivity; would not differentiate the user experience.

- **Alternative 2: Rules-Only System (No ML)**  
  Predefined rules for weather and traffic conditions.  
  *Rejected* — does not scale with user diversity or continuous learning needs.

- **Alternative 3: Fully Cloud-Hosted AI (No Edge)**  
  Simplifies training and operations but increases latency and dependency on connectivity.  
  *Rejected* — unacceptable in offline-prone or high-demand areas.

## Challenges/Trade-offs

- **Latency vs. Infrastructure Complexity**
 
 text
  
- **Scalability vs. Local Resource Constraints**

 text
 
  
- **Data Synchronization & Consistency**

 text
 
  
- **Operational Overhead**

 text
 
  
- **Security, Privacy & Compliance**

 text
 

> Additional **MCP Trade-offs to watch**
> - Eventual consistency: brief windows where different edges run different versions—mitigate with region-scoped rollouts.
> - Cold start vs. footprint: more cached variants reduce cold starts but increase storage.
> - Telemetry lag: decisions in MCP rely on timely edge metrics; buffer + backfill when offline.

## Conclusion
We will deploy a **hybrid AI route optimization service** leveraging ML for personalization, RL for continuous improvement, and rule-based logic for safety and compliance. The system will initially integrate with Google Maps for baseline route data and progressively transition to in-house predictive models. 
To ensure governance, traceability, and safe rollout of models, we will apply a cloud-based Model Control Plane (MCP) — managing model versions, deployment policies, telemetry, and automated rollback in case of degradation. This enables consistent control of model lifecycles across both cloud and edge environments, ensuring reliability and compliance throughout continuous updates.
Edge caching will ensure responsiveness. The design supports modular extension for future contexts such as energy consumption.
