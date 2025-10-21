# ADR-0014 — AI Model Selection for Cleanliness Assessment

## Context

This ADR selects the approach for AI-based cleanliness assessment from customer return photos (see FR-AI-001). The system must classify cleanliness levels (Acceptable, Minor, Major, Severe) and support a human-in-the-loop review for low-confidence cases before charging cleaning fees. Requirements include:

- Fast time-to-value with managed services (inherit ADR-0003 Vertex AI as the core AI platform).
- Governed model lifecycle (inherit ADR-0009 Model Management on GCP).
- Continuous evaluation and deployment gating (inherit ADR-0011 Model Evaluation Flow).
- EU data residency, encryption, and IAM (inherit ADR-0001 GCP posture).
- Reuse of GCP-native data platform for storage and training datasets (inherit ADR-0006 Knowledge Management).

Operational constraints:
- Photo capture occurs at return (FR-CV-007) and by crew (FR-CV-027). Latency target per image: < 2–5 seconds online inference.
- Confidence thresholds must minimize false positives to protect customer experience; low-confidence flows route to manual review.
- Cost control and maintainability are required across ~hundreds of thousands of monthly photos.

## Decision

Adopt a staged approach using Vertex AI managed services first, with a clear upgrade path to a custom model if accuracy targets are not met.

1) Start with Vertex AI AutoML Image Classification (multi-label) to classify cleanliness severity (Acceptable, Minor, Major, Severe):
- Managed training and hosting minimize MLOps overhead (aligns with ADR-0003).
- Integrates with Model Registry, Pipelines, and Endpoints for lifecycle (ADR-0009).
- Evaluation and deployment gating via ADR-0011 (pre/post-deploy metrics, A/B, threshold tuning).
- Data stored in GCS/BigQuery with EU residency and IAM per ADR-0001 and ADR-0006.

2) If evaluation shows persistent gaps to business targets (e.g., false positives >5%, precision/recall below thresholds), evolve to a custom detection model (EfficientDet/YOLO) hosted on Vertex AI:
- Custom object detection can localize stains/debris and improve explainability.
- Use the same MLOps and evaluation stack (ADR-0009, ADR-0011) for portability and governance.

3) Apply human-in-the-loop policy:
- High confidence: auto-accept or auto-fee per policy.
- Medium confidence: send to human reviewer queue (crew/ops) with SLA.
- Low confidence: default to Acceptable (customer benefit) and log for retraining.

Success criteria:
- Latency: P95 inference ≤ 2.5s per photo (online), batch scoring allowed for non-blocking flows.
- Quality: False positive rate < 5%; precision/recall ≥ defined thresholds by severity class.
- Governance: Model versions evaluated and promoted only when passing gates (ADR-0011).

## Key Differentiators

- **Time-to-value vs. accuracy**  
  Start with AutoML for rapid deployment; graduate to custom detection only if needed.

- **MLOps alignment**  
  Leverage Vertex AI Registry, Pipelines, and Endpoints for consistent governance (ADR-0009).

- **EU compliance & security**  
  GCS/BigQuery with regional controls, CMEK, and IAM (ADR-0001, ADR-0006).

- **Human trust & UX safeguards**  
  Confidence thresholds, reviewer queues, and auditable overrides prevent unfair charges.

- **Portability**  
  Keep datasets, metrics, and model artifacts exportable to reduce lock-in risk.

## Alternatives Considered

- **Vertex AI AutoML Image Classification (Selected initial approach)**  
  Pros: fastest to production, fully managed, integrates with Registry/Pipelines/Eval.  
  Cons: limited architecture control; may plateau on hard edge cases.

- **Custom object detection (EfficientDet/YOLO) on Vertex AI**  
  Pros: higher control, can localize stains/debris; potentially higher accuracy and explainability.  
  Cons: more ML expertise and ops overhead; longer time-to-value; tuning required for latency.

- **Off-the-shelf Vision APIs (e.g., Cloud Vision, Rekognition, Cognitive Services)**  
  Pros: minimal setup, commodity pricing.  
  Cons: generic models underperform for domain-specific cleanliness; privacy and cross-cloud concerns; deviates from ADR-0001/0003.

- **On-device inference (mobile)**  
  Pros: lowest latency, offline capability.  
  Cons: model size/compatibility limits; complex update and privacy story; consider later if strong ROI.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|--|--|--|
| Accuracy shortfall | Model misclassifies borderline cases → unfair fees or missed revenue | Use HITL workflow; conservative thresholds; continuous evaluation (ADR-0011); retraining cadence |
| Bias & data quality | Non-representative images (lighting, angles, vehicle types) | Data audits; augmentation; active learning; labeling guidelines; fairness checks (ADR-0006) |
| Latency & cost | High per-image latency or serving cost at scale | Choose efficient architectures; autoscaling; cache baselines; consider batch for non-critical steps |
| Vendor lock-in | Deep Vertex usage | Keep artifacts exportable; document fallback hosting; isolate business logic (ADR-0003/0009) |
| Privacy & GDPR | Photo retention and data rights | Enforce EU residency, retention policy ADR (TBD), CMEK, access controls (ADR-0001) |
| Drift over time | Changing usage, camera quality, seasons | Ongoing monitoring, periodic re-labeling, drift alerts, scheduled retraining (ADR-0011) |
| Explaining decisions | Customer disputes require evidence | Store annotations; localize features (in custom phase); retain review logs for audit |

## Conclusion

Proceed with Vertex AI AutoML Image Classification for cleanliness severity as the initial implementation, governed by Vertex AI Model Registry/Pipelines and evaluated via the Model Evaluation Flow. Use human-in-the-loop for medium/low-confidence decisions. If targets are not met, migrate to a custom object detection model hosted on Vertex AI while retaining the same MLOps and evaluation framework. This approach balances speed, maintainability, customer trust, and long-term accuracy.
