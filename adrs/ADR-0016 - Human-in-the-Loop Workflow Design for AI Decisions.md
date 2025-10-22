# ADR-0016 — Human-in-the-Loop Workflow Design for AI Decisions

## Context

Our AI features (FR-AI-001 Vehicle Cleanliness Verification & Cleaning Fees; FR-AI-003 AI-Assisted Damage Detection) require human verification when AI confidence is low or cases are ambiguous. The workflow must prevent unfair customer charges, ensure timely decisions, capture reviewer feedback for model improvement, and comply with privacy and audit requirements.

This ADR defines a standardized HITL (Human-in-the-Loop) workflow for vision decisions, grounded in:
- [ADR-0001](ADR-0001%20-%20GCP%20as%20main%20cloud%20provider.md) (GCP posture): EU residency, encryption, IAM, audit logging
- [ADR-0003](ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md) (Vertex AI platform): managed model serving and integrations
- [ADR-0006](ADR-0006%20-%20Knowledge%20Management%20on%20GCP%20with%20Vertex%20AI%20Integration.md) (Knowledge platform): storage of images, labels, and review artifacts
- [ADR-0009](ADR-0009%20-%20GenAI%20Model%20Management%20on%20GCP.md) (Model management): versioned models and dataset lineage
- [ADR-0010](ADR-0010%20-%20Evaluation%20and%20Adoption%20of%20Vertex%20AI%20Agent%20Builder%20for%20AI%20Service%20and%20Orchestration.md) (Agent Builder): orchestration for review routing and decision assistance
- [ADR-0011](ADR-0011%20-%20Model%20Evaluation%20Flow%20using%20Vertex%20AI.md) (Model evaluation): gating criteria and continuous monitoring
- [ADR-0014](ADR-0014%20-%20AI%20Model%20Selection%20for%20Cleanliness%20Assessment.md) (Cleanliness model selection): confidence bands and escalation
- [ADR-0015](ADR-0015%20-%20Training%20Data%20Strategy%20and%20Bias%20Mitigation%20for%20Vision%20Models.md) (Training data strategy): feedback loop to improve datasets

## Decision

Adopt a hybrid workflow with policy-driven confidence bands and a dedicated review queue integrated with the ops dashboard/crew app, orchestrated via Agent Builder where appropriate.

1) Confidence Bands & Actions (per model/version)
- High confidence: Auto-accept/auto-fee per policy; log rationale and evidence snapshot.
- Medium confidence: Route to human review queue with SLA. Reviewer sees AI prediction, explanation cues (saliency/region), and evidence gallery; must confirm or override with reason code.
- Low confidence: Default to customer-favorable outcome (no fee / defer decision); flag for dataset enrichment.

2) Review Queue & SLA
- Separate queues per use case (cleanliness vs damage) with priority rules (e.g., imminent next booking).  
- SLA targets: Cleanliness P95 ≤ 2 hours; Damage P95 ≤ 1 hour during business hours; off-hours escalation to on-call.  
- Auto-reminder and escalation if nearing SLA; optional batch reviews for similar cases.

3) Reviewer UX & Audit
- Single-pane view: Side-by-side photos (baseline vs return), annotations, AI scores, and confidence band.  
- One-click actions: Approve/Reject/Request more photos; reason codes (taxonomy) and free-text notes.
- Full audit trail: who/when/what, model version, thresholds, evidence links; exportable to BigQuery.

4) Orchestration & Integration
- Use Vertex AI Agent Builder to route cases, suggest actions, and collect structured feedback when beneficial (ADR-0010).  
- Integrate with Crew App (FR-CV-027) for additional photos or on-site verification.  
- Publish decisions to Payment Service (apply/waive fees) and to Knowledge Platform for analytics.

5) Continuous Improvement Loop
- Capture reviewer overrides and edge cases as labeled examples for retraining (ADR-0015).  
- Maintain "golden review" subsets for calibration; periodically re-score with new model versions.  
- Feed outcomes into evaluation pipelines (ADR-0011) to tune thresholds and gating criteria.

6) Privacy & Security
- Enforce least-privilege IAM, EU-only storage, and minimal retention; blur PII when not required.  
- Redact sensitive notes in exports; log access to evidence and decisions.  
- Provide customer-facing transparency for disputes with evidence references.

Success Metrics
- Resolution SLA adherence ≥ 95%.  
- Reduction in false positives by ≥ 30% compared to pre-HITL baselines.  
- Reviewer agreement ≥ κ 0.8 on sampled cases.  
- Model improvement: measurable precision/recall gains every 1–2 training cycles on golden sets.

## Key Differentiators

- **Customer-first safeguards**  
  Default favorable outcomes at low confidence with auditable reasoning reduce unfair charges.

- **Operational readiness**  
  Queues, SLAs, reason codes, and integrated UX make reviews fast, consistent, and measurable.

- **Tight MLOps integration**  
  Reviewer feedback flows directly into datasets and evaluation pipelines, accelerating model improvement.

## Alternatives Considered

- **Fully automated decisions**  
  Pros: lowest latency and cost.  
  Cons: higher risk of unfair charges and churn; no guardrails for ambiguous cases.

- **Manual-only inspection**  
  Pros: high accuracy in nuanced scenarios.  
  Cons: slow, expensive, inconsistent; poor scalability.

- **Post-charge review window only**  
  Pros: simple process.  
  Cons: harms trust; refunds after-the-fact increase support burden and frustration.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|--|--|--|
| Latency & backlog | Reviews may miss SLAs during peaks | Priority queues; batching; escalation; staffing plans; sampling when safe |
| Reviewer bias | Inconsistent decisions between reviewers | Guidelines, calibration sessions, κ monitoring; golden reviews; reason codes |
| Cost | Human review time and tooling | Focus on medium-confidence only; automation assists; optimize UI for speed |
| Privacy | Exposure of customer images and notes | Least privilege IAM; PII blurring; audit logs; data minimization |
| Complexity | Orchestration and integration overhead | Use Agent Builder patterns; shared components; iterative rollout |
| Threshold drift | Confidence thresholds degrade over time | Continuous evaluation (ADR-0011); periodic recalibration; A/B tests |

## Conclusion

Implement a standardized, auditable HITL workflow with confidence bands, review queues, SLAs, and integrated feedback loops. Orchestrate with Vertex AI Agent Builder where appropriate, and connect outputs to payment, disputes, and MLOps pipelines. This hybrid approach protects customer trust while enabling continuous model improvement and operational scale.
