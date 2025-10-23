# ADR-0015 — Training Data Strategy and Bias Mitigation for Vision Models

## Context

AI features depend on reliable computer vision models for:
- FR#2L: Vehicle cleanliness verification (fee decisions)
- FR#2M: Pre-rental damage detection (customer protection, dispute prevention)

These use cases require large, representative, and governed image datasets captured by customers and crew in diverse environments (lighting, weather, locations, devices). Risks include data bias (under-representation of specific vehicle colors, interiors, lighting), privacy/GDPR concerns, and model drift over time.

This ADR establishes a training data strategy aligned with:
- ADR-0001 (GCP posture): EU-only storage, encryption (CMEK), IAM, privacy-by-design
- ADR-0003 (Vertex AI platform): managed data labeling and ML pipelines
- ADR-0006 (Knowledge platform): BigQuery/Cloud Storage/Dataflow for ingestion, curation, and retrieval
- ADR-0009 (Model management): dataset and model lineage via Registry/Pipelines
- ADR-0011 (Model evaluation): dataset splits, golden sets, continuous evaluation & drift monitoring

Goals
- High-quality, unbiased datasets with auditable provenance
- Efficient, cost-aware labeling and iteration cycles
- Continuous improvement via active learning and drift detection

## Decision

Adopt a governed, iterative data pipeline on GCP with Vertex AI labeling, stratified sampling, augmentation, and bias checks; enforce privacy controls and EU residency. Key elements:

1) Data Ingestion & Curation
- Store raw images in GCS (EU regions) with CMEK and object-level retention policies (align with FR-CV-019, Photo Retention ADR TBD).  
- Ingest via Dataflow/Cloud Run; attach metadata (device, time, geo bucket, lighting proxy, vehicle type) into BigQuery.  
- De-identification: blur faces/license plates during preprocessing where not required for task; maintain reversible mapping only for disputes under strict access.

2) Labeling Workflow
- Use Vertex AI Data Labeling for ground-truth with expert vendor support for consistency; adopt clear guidelines and exemplars.  
- Seed a “golden set” (fixed reference set) for evaluation and reviewer calibration.  
- Introduce double-blind labeling on 10–20% of samples; measure inter-annotator agreement (Cohen’s κ ≥ 0.8 target).

3) Dataset Design & Versioning
- Maintain datasets as versioned snapshots in BigQuery/GCS with semantic tags: train/val/test, golden, bias-probe sets.  
- Apply stratified sampling across conditions: lighting (day/night), weather (dry/wet), color/material, interior vs exterior, city/garage.  
- Track lineage: dataset manifest (BQ table) links each model version to its dataset hash and labeling policy (via Vertex Pipelines/Registry).

4) Bias Mitigation & Quality Controls
- Define fairness/bias metrics per use case (e.g., false positive rate per lighting bin; cleanliness severity confusion matrix stability).  
- Run periodic bias probes on under-represented strata; require stability thresholds before promotion.  
- Use augmentation (brightness/contrast, blur, noise, perspective) to balance representation; avoid unrealistic transformations.  
- Consider domain-specific synthetic data to bolster rare classes (e.g., severe mess, specific interior trims). 

5) Active Learning & Continuous Improvement
- Harvest hard negatives/false positives from production; prioritize for relabeling.  
- Schedule quarterly dataset refresh; auto-open labeling tasks when drift detectors (ADR-0011) trigger.  
- Keep a compact “shadow” golden set to monitor regressions across model versions.

6) Privacy, Security, and Governance
- EU-only storage; minimize retention (see Photo Retention ADR TBD) and apply access via least privilege (IAM).  
- Consent flows for image capture; clear in-app disclosures; apply PII-minimizing capture angles where feasible.  
- Audit logs for dataset access and label changes; incident process for data handling issues.

Success criteria
- Labeling quality: κ ≥ 0.8; label drift ≤ defined thresholds per quarter.  
- Bias metrics: No subpopulation has >2× false positive rate vs overall; severity calibration within ±10% per stratum.  
- Iteration velocity: Median cycle (ingest→label→train) ≤ 2 weeks at steady state.

## Key Differentiators

- **Governed data lifecycle**  
  Versioned datasets with lineage and golden sets enable auditable, reproducible training.

- **Bias-aware sampling & metrics**  
  Stratified sampling and stability checks reduce overfitting to easy conditions and surfacing of fairness gaps.

- **Operational efficiency**  
  Managed labeling with exemplars, double-blind QA, and active learning shorten improvement loops.

- **Privacy by design**  
  EU residency, de-identification, minimal retention, and consent-first capture protect users and reduce risk.

## Alternatives Considered

- **Fully in-house labeling platform**  
  Pros: full control; tailored UX for domain.  
  Cons: higher engineering overhead; slower to reach quality and throughput; less integrated with Vertex pipelines.

- **Third-party labeling vendors only**  
  Pros: scale quickly with expert raters.  
  Cons: governance and privacy complexity; must enforce EU-only handling and DPA terms; still need internal QA/golden sets.

- **Auto-labeling with weak supervision only**  
  Pros: low manual cost.  
  Cons: error propagation, lower trust; still requires human QA and golden set validation.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|--|--|--|
| Privacy & GDPR | Images can include faces/plates; risk of over-retention | Blur PII; EU-only storage; retention policy ADR; DLP audits; consent UX |
| Dataset bias | Under-represented lighting/interiors/locations create unfair outcomes | Stratified sampling; augmentation; bias probes; promotion gates |
| Label quality | Inconsistent labels degrade model | Double-blind QA; κ targets; reviewer training; golden sets |
| Cost & time | Labeling at scale can be expensive and slow | Active learning; prioritize hard cases; vendor SLAs; sample efficiency |
| Drift | Seasonal/device changes shift data | Drift detectors; scheduled refresh; continuous evaluation (ADR-0011) |
| Vendor lock-in | Dependence on Vertex tooling | Keep data in open formats; exportable manifests; document alternative flows |

## Conclusion

Implement a governed, EU-compliant training data pipeline using Vertex AI labeling, BigQuery/GCS curation, stratified sampling, augmentation, bias probes, and active learning. Tie datasets to model versions via Registry/Pipelines and gate promotions with stability and fairness checks. This balances model quality, fairness, privacy, and iteration speed for our cleanliness and damage detection use cases.
