# ADR-0001 — GCP as main cloud provider
## Context
We are building a micro-mobility solution (e-bikes, scooters, car-sharing) requiring real-time tracking, routing, payments, analytics, and scalable fleet management.

## Decision
We choose **Google Cloud Platform (GCP)** as the core cloud provider.

## Key Differentiators

- **Seamless Maps & Location Services Integration**  
  Native Google Maps APIs for routing, geofencing, traffic-aware navigation, and real-time vehicle tracking. No additional integration layer needed, unlike AWS or Azure.

- **High-Performance Real-Time Data Handling**  
  BigQuery, Pub/Sub, and Dataflow allow near-instant analytics and event processing across fleets, enabling smarter fleet management and predictive maintenance.

- **Scalability & Global Network**  
  GCP’s global backbone ensures low-latency services for users in multiple cities, which is crucial for real-time micromobility operations.

- **AI/ML Integration Made Easy**  
  Vertex AI, AutoML and Gemini enable demand prediction, route optimization, and user behavior insights without managing separate ML infrastructure.

- **Operational Efficiency & Dev Experience**  
  Tight integration between services reduces engineering overhead, accelerates development, and ensures smooth deployment pipelines.

## Alternatives Considered
- **AWS**: Strong compute and storage, but requires third-party mapping integration; heavier ML setup.  
- **Azure**: Enterprise-friendly, but mapping and real-time fleet analytics require more custom work.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|--|--|--|
| Vendor lock-in                   | Heavy reliance on GCP location and AI services may make migration costly or complex.                        | Use abstraction layers for critical services where feasible; document fallback options.          |
| Feature dependency               | Dependence on specific GCP features (Gemini, Vertex AI) may limit flexibility if APIs change or pricing shifts. | Maintain minimal viable integration surface; monitor vendor updates; design fallback pathways.   |
| Cost predictability              | High-volume Pub/Sub, BigQuery, and Maps usage can lead to unpredictable costs.                              | Implement cost alerts, quota management, and optimize ingestion pipelines early.                 |
| Regional availability            | GCP services may have latency or compliance limitations in some markets.                                    | Deploy regionally and use multi-region architecture for redundancy.                             |
| Skills & ecosystem               | GCP ecosystem may be less familiar to new hires compared to AWS or Azure.                                   | Training plan, standardized infrastructure templates, and internal documentation.                |
| Integration flexibility          | Tightly coupled stack may be less flexible when adding non-Google services.                                 | Keep messaging and storage layers as portable as possible; avoid unnecessary hard coupling.      |


## Competitors
Some of the direct competitors leverage GCP that confirms it as a proven technology stack
- Bird : https://www.bird.co/blog/bird-vps-powered-google-change-scooter-parking-forever/
- Dott : https://ridedott.com/terms-and-conditions/uae/#:~:text=Dott's%20platform%20operates%20entirely%20on,access%20to%20hardware%20or%20data
Also GCP is popular among car sharing companies, e.g. Zoomcar: https://www.linkedin.com/posts/themainstream_themainstream-zoomcar-googlecloudai-activity-7359225564924100608-zxnx/

## Conclusion
GCP is the most **integrated, real-time, and ML-ready platform** for micro-mobility solutions. Its native Maps APIs, global network, and streamlined data/AI services make it the optimal choice over AWS and Azure.
