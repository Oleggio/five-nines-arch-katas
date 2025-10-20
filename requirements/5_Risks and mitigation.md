# Risks & Mitigation Strategies

> Note: This is a common risk registry for main business concerns. Detailed risk mitigation strategy per each technology choice are to be found in corresponding ADRs. 

| Risk Area | Description | Mitigation |
|-----------|-------------|------------|
| Vendor lock-in | Focus on GCP services only (single cloud), deep use of Vertex AI | Use abstraction layers for critical services where feasible; document fallback options |
| Cost/budgeting | Managed features, MLOps and orchestration can be expensive | Implement cost alerts, quota management, and optimize ingestion pipelines early. Iteratively optimize usage. |
| Team skills | Vertex AI, IoT expertise required | Training and documentation |
| GCP Data residency | Compliance with data protection laws |	Regional deployment and policies |
| Regional availability | GCP services may have latency or compliance limitations in some markets | Regional deployment, multi-region architecture for redundancy |
| IoT messaging reliability | To guarantee delivery, MQTT QoS handling and Pub/Sub acknowledgment semantics must be carefully aligned. | Enforce QoS 1 or 2 where needed, ensure idempotent message handling, and define retry strategies.|
| Integration flexibility on GCP | Tightly coupled stack may be less flexible when adding non-Google services.| Keep messaging and storage layers as portable as possible; avoid unnecessary hard coupling |
| GCP/AI Feature dependency | Dependence on specific GCP features (Gemini, Vertex AI) may limit flexibility if APIs change or pricing shifts | Maintain minimal viable integration surface; monitor vendor updates; design fallback pathways |
| Overall system scaling | High query loads | Capacity planning and autoscaling |
| Operational Overhead | Centralized cloud processing requires monitoring and continuous observability. | Integrate with cloud monitoring stack |
| AI Model Accuracy | Models may degrade if environment changes rapidly | Continuous retraining pipeline with drift detection |
