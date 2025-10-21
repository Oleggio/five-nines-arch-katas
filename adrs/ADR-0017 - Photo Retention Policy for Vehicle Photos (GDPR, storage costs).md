# ADR-0017 — Photo Retention Policy for Vehicle Photos (GDPR, storage costs)

## Context

The platform captures and processes photos in multiple flows:
- FR-CV-007: Customer return verification (parking bay, charger connection, cleanliness)
- FR-AI-003: Pre-rental inspection (customer-provided) and operations follow-ups
- FR-AI-001: Cleanliness assessment (AI + human review) using return photos
- Crew/operations photos for manual inspections and disputes

These photos contain personal data (license plates, potentially people, locations) and thus fall under GDPR. We must balance:
- Legal compliance (lawful basis, minimization, retention limits, right to erasure)
- Operational needs (dispute resolution, fraud prevention, safety, quality audits)
- AI/analytics needs (model training/improvement)
- Cost control (storage classes, lifecycle policies, deletion)

This ADR defines a unified photo retention and lifecycle policy across capture flows, storage locations, and derived datasets.

Related decisions: ADR-0001 (GCP posture), ADR-0003 (Vertex AI), ADR-0006 (Knowledge Platform), ADR-0009 (Model Management), ADR-0011 (Model Evaluation), ADR-0014 (Cleanliness model), ADR-0015 (Training data & bias), ADR-0016 (HITL workflow).

## Decision

Adopt a tiered retention policy with strict separation between:
1) Operational originals (customer/crew photos) for core business processes and disputes
2) Working derivatives (thumbnails, annotated review copies)
3) Anonymized training/evaluation datasets for AI model improvement

Apply EU-only storage, strong IAM, CMEK encryption, and automated lifecycle management to minimize cost and risk while preserving business utility.

## Policy Details

### Storage locations
- Originals and derivatives: Google Cloud Storage (GCS) in EU multi-region (europe) or regional EU, with CMEK.
- Training/eval datasets: GCS + BigQuery in EU locations, CMEK. Catalog in Vertex AI Model Registry and Data Catalog.

### Retention periods
- Operational originals (customer/crew uploads)
  - Default: 180 days
  - Extended hold: Up to 2 years only when linked to an active dispute, chargeback, insurance claim, or legal request (with case ID and time-bound legal hold)
- Working derivatives (thumbnails, annotated copies used for review/UIs)
  - 90 days (auto-delete when parent original is deleted)
- AI training/evaluation datasets (anonymized)
  - Retain while models are active and data remains necessary and proportionate; review annually
  - Remove links to identifiable metadata; keep only minimal labels and technical attributes

### Lawful basis and purpose limitation
- Operational originals: Contract necessity and legitimate interest (fraud/dispute prevention). Purposes: return verification, inspection, dispute resolution.
- Training/eval datasets: Legitimate interest with strict minimization and opt-out where applicable; obtain explicit notice in app privacy policy and settings.

### Data minimization and anonymization
- On ingestion: Automatically blur faces and license plates when not required to be visible; retain unblurred originals only where strictly necessary (e.g., law enforcement requests) and shielded by tighter IAM.
- Strip EXIF data except timestamp and coarse GPS needed for verification; store precise coordinates separately under access-controlled metadata if required.

### Lifecycle controls (automated)
- GCS Object Lifecycle Management:
  - Transition originals to Nearline at 30 days, Coldline at 90 days; delete at 180 days unless under legal hold
  - Thumbnails/derivatives: delete at 90 days
  - Anonymized training copies: remain in Standard/Nearline depending on frequency of use; re-validate necessity annually
- Legal holds:
  - Apply object holds for active disputes/claims; release on case closure with 30-day grace
- Deletes & DSARs (data subject access/deletion requests):
  - Index photos by rental session, customer ID, and vehicle ID; enable targeted deletion across originals and derivatives

### Access controls & audit
- IAM: Least-privilege service accounts per workload; break-glass access controlled by approval workflow
- CMEK: Project-level customer-managed keys; rotate annually; use per-bucket keys for blast-radius reduction
- Audit: Cloud Audit Logs for access and deletes; export to centralized log sink with retention 400 days

### Cost controls
- Storage classes: Standard → Nearline (30d) → Coldline (90d) for originals; derivatives short-lived (90d)
- Compression: Enforce JPEG quality setting and server-side compression for thumbnails
- Deduplication: Content-hash to avoid storing duplicates from retries/resubmits
- Quotas/alerts: Per-bucket size and daily ingest alerts in Cloud Monitoring

### Interactions with AI pipelines
- Training datasets created via governed pipelines (ADR-0009) that:
  - Materialize anonymized copies to dedicated buckets with restricted IAM
  - Record lineage in Data Catalog; track consent/notice status
  - Support purge-by-policy for categories, vehicles, time ranges
- Evaluation artifacts (confusion matrices, samples) exclude personal identifiers; store for model governance per ADR-0011

## Alternatives Considered
- Keep all photos indefinitely in low-cost storage (Archive) to minimize risk of losing evidence
  - Rejected: Violates GDPR storage limitation and increases breach impact and cost
- Immediate deletion after verification
  - Rejected: Insufficient for disputes, chargebacks, insurance, and quality audits
- Centralized single bucket without separation of duties
  - Rejected: Harder to enforce least privilege and minimization; larger blast radius

## Risks & Trade-offs
| Risk Area | Description | Mitigation |
|--|--|--|
| Evidence unavailability | Deletion at 180 days may hinder late disputes | Clear dispute windows in T&Cs; allow legal holds; communicate timelines to partners |
| AI performance | Anonymization may remove useful signals | Keep necessary technical attributes; evaluate impact; tune blurring to preserve non-identifying cues |
| Cost spikes | High photo volumes increase costs before lifecycle transitions | Early Nearline/Coldline transitions; compression; quotas and alerts |
| DSAR complexity | Deleting across originals, derivatives, and training copies | Robust indexing and lineage; automation in purge pipelines |
| Access misuse | Over-broad access to raw images | Least-privilege IAM, per-bucket CMEK, audit logs, approval workflows |

## Implementation Notes
- Buckets: `photos-originals-eu`, `photos-derivatives-eu`, `photos-ml-anon-eu` with per-environment suffixes
- Metadata schema: `{rentalId, customerId, vehicleId, flow (return|inspection|crew), timestamp, locationRef, caseId?}`
- Lifecycle policies: IaC (Terraform) with unit tests; review quarterly
- App UX: Privacy notice at capture; link to retention policy and DSAR process

## References
- ADR-0001, ADR-0003, ADR-0006, ADR-0009, ADR-0011, ADR-0014, ADR-0015, ADR-0016
- GDPR Art. 5 (data minimization, storage limitation), Art. 6 (lawful basis), Art. 17 (erasure)

## Conclusion
Adopt a tiered, automated photo retention policy with EU-only storage, strict IAM/CMEK, anonymized training datasets, and lifecycle transitions to control cost. This satisfies GDPR storage limitation and supports disputes, model improvement, and customer trust while limiting risk and spend.
