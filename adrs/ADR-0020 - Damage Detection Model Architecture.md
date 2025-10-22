# ADR-0020 — Damage Detection Model Architecture

## Context

Damage detection is a critical AI feature for pre- and post-rental vehicle inspection (FR-AI-003), supporting automated fine assessment, dispute prevention, and customer protection. The model must identify and localize scratches, dents, cracks, paint chips, broken glass, and other damage from customer and crew photos, with high accuracy, explainability, and real-time inference capability.

This ADR defines the architecture and selection process for the damage detection ML model, building on established decisions:
- ADR-0003: Vertex AI as core platform for AI/ML workloads
- ADR-0009: Model lifecycle management using Vertex AI Model Registry
- ADR-0011: Model evaluation gates and continuous monitoring
- ADR-0014: Staged ML approach (AutoML → custom model if needed)
- ADR-0015: Training data strategy, labeling quality, and bias mitigation
- ADR-0016: Human-in-the-loop workflow for low-confidence cases
- ADR-0017: Photo retention policy and GDPR compliance

Business requirements and constraints:
- FR-AI-003: Pre-rental inspection with AI-assisted damage detection, target 80% detection rate with <10% false positives
- NFR-AI-001: Model accuracy ≥85% precision, ≥80% recall for damage detection
- NFR-AI-002: Inference latency ≤5 seconds per photo (p95)
- NFR-AI-006: Explainability required (annotated photos with bounding boxes)
- NFR-AI-008: Graceful degradation with human review fallback

Industry best practices and standards:
- COCO dataset format for object detection training/evaluation
- IoU (Intersection over Union) ≥0.5 for bounding box accuracy
- mAP (mean Average Precision) as standard metric for multi-class detection
- ONNX or TensorFlow Lite for model portability (NFR-AI-004)

## Decision

Adopt a **staged approach** with clear upgrade path:

### Phase 1: Vertex AI AutoML Vision Object Detection (Initial Production)
- **Start with managed service** for rapid time-to-value and minimal MLOps overhead
- Train on COCO-formatted dataset with damage categories: scratch, dent, crack, paint_chip, broken_glass, tire_damage, panel_misalignment
- Target metrics:
  - mAP@0.5 ≥0.75 (mean Average Precision at IoU threshold 0.5)
  - Per-class precision ≥85%, recall ≥80%
  - Inference latency ≤5s per image at 1280×720 resolution
- Confidence thresholds (aligned with ADR-0016):
  - High confidence (≥85%): Auto-flag damage, notify customer/crew
  - Medium confidence (70-85%): Route to human review queue
  - Low confidence (<70%): Log for model improvement, no auto-flag
- Integration:
  - Deployed on Vertex AI Prediction endpoint with autoscaling
  - Registered in Vertex AI Model Registry (ADR-0009)
  - Evaluation gates per ADR-0011 before production promotion

### Phase 2: Custom CNN Object Detection (If KPIs Not Met)
- **Escalate to custom model** if:
  - mAP < 0.75 after 3 months in production
  - False positive rate > 10% consistently
  - Latency consistently exceeds 5s at p95
  - Business feedback indicates unacceptable customer friction
- Candidate architectures:
  - **EfficientDet-D2** (balanced speed/accuracy, ~7M parameters)
  - **YOLOv8-Medium** (real-time optimized, mobile-friendly)
  - Pre-trained on COCO, fine-tuned on vehicle damage dataset
- Training approach:
  - Transfer learning from ImageNet/COCO base models
  - Augmentation: rotation, brightness, occlusion, weather effects
  - Multi-scale training for robustness across photo resolutions
- Deployment:
  - Vertex AI Custom Prediction with GPU (NVIDIA T4 or A10)
  - Model optimization: TensorRT or ONNX Runtime for latency reduction
  - A/B testing against AutoML baseline (champion/challenger)

### Model Output Format
All models (AutoML or custom) must output standardized JSON:
```json
{
  "predictions": [
    {
      "damage_type": "dent",
      "confidence": 0.92,
      "bounding_box": {"x": 120, "y": 80, "width": 150, "height": 100},
      "severity": "moderate"
    }
  ],
  "overall_confidence": 0.88,
  "review_required": false,
  "model_version": "damage-detect-v1.3.2"
}
```

### Training Data Requirements (ADR-0015)
- Minimum 10,000 labeled images per damage category
- Stratified across: vehicle types (sedan, van, SUV), lighting conditions, camera angles, damage severity
- Labeling quality: Cohen's κ ≥ 0.80 (double-blind labeling)
- Bias audits: Performance parity across vehicle makes/models, geographies
- Active learning: Flag borderline predictions for human labeling and retraining

### Explainability and Audit (ADR-0016)
- Annotated photos with bounding boxes overlaid on original images
- Confidence scores visible to customers and operations teams
- Human override logging with reason codes
- Dispute evidence package includes: original photo, annotated photo, model version, confidence, reviewer notes

### Compliance and Data Lifecycle (ADR-0017)
- Training data anonymized (license plates/faces blurred)
- Inference photos retained per 180-day policy with CMEK encryption
- Model artifacts versioned and stored in EU regions (GDPR compliance)

## Key Differentiators

- **Staged Pragmatism**  
  Start with AutoML for rapid delivery; upgrade to custom CNN only if business metrics justify the increased complexity.

- **Explainability First**  
  Bounding boxes and confidence scores provide transparency for customer disputes and operational audits.

- **MLOps Integration**  
  Seamless integration with Vertex AI Registry, Pipelines, and Evaluation gates ensures governance and rollback capability.

- **Human-AI Collaboration**  
  Confidence-based routing to human review prevents customer harm while improving model via active learning.

- **Portability Consideration**  
  Custom models trained with exportable formats (ONNX) reduce vendor lock-in risk (NFR-AI-004).

## Alternatives Considered

- **Vertex AI AutoML Vision Only (No Custom Escalation)**  
  Pros: Simplest to operate; fully managed; minimal ML expertise required.  
  Cons: May plateau at ~75% mAP; limited control over architecture for edge cases; harder to optimize for specific damage types.  
  Rejected: Lacks upgrade path if business KPIs are not met.

- **Custom CNN from Day One (EfficientDet/YOLO)**  
  Pros: Maximum control; potentially higher accuracy; optimized for latency.  
  Cons: Longer time-to-value; requires ML engineering team; higher operational complexity.  
  Rejected: Premature optimization; staged approach reduces risk.

- **Hybrid Ensemble (AutoML + Custom CNN)**  
  Pros: Combine strengths; use AutoML for common cases, custom for rare damage.  
  Cons: Operational complexity; increased latency; harder to debug predictions.  
  Rejected: Added complexity not justified unless neither single approach meets KPIs.

- **Off-the-Shelf Damage Detection APIs (e.g., Tractable, Monk AI)**  
  Pros: Instant deployment; domain-specialized; proven accuracy.  
  Cons: Vendor lock-in; data privacy concerns (GDPR); cost per API call; limited customization.  
  Rejected: Conflicts with ADR-0003 (Vertex AI platform) and ADR-0001 (GCP-first posture).

- **On-Device Inference (Mobile TensorFlow Lite)**  
  Pros: Lowest latency; offline capability; no photo upload needed.  
  Cons: Model size limits (~50MB); inconsistent accuracy across devices; complex model updates; no centralized monitoring.  
  Rejected: Considered for future optimization if latency becomes critical.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|-----------|-------------|------------|
| Accuracy shortfall | AutoML may not reach 85% precision/80% recall on subtle damage (hairline cracks, minor scratches) | Use confidence thresholds; route borderline cases to human review; augment training data; escalate to custom CNN if persistent |
| False positives | Model flags shadows, reflections, or dirt as damage → unfair customer charges | Conservative confidence thresholds (≥85% for auto-flag); human review for 70-85%; continuous evaluation (ADR-0011); dispute resolution with override |
| Latency spikes | Real-time inference may exceed 5s under load or for high-res photos | Autoscaling Vertex AI endpoints; image preprocessing (downscale to 1280×720); GPU for custom models; batch processing for non-critical flows |
| Data bias | Model underperforms on rare vehicle types, poor lighting, or specific damage types | Stratified dataset (ADR-0015); bias audits per vehicle type/geography; active learning to fill gaps; periodic retraining |
| Vendor lock-in | Deep Vertex AI integration limits portability | Export custom models to ONNX; keep training datasets in open formats; document fallback hosting options (ADR-0003/0009) |
| GDPR compliance | Training on customer photos without proper anonymization or retention | Follow ADR-0017 (blur faces/plates, 180-day retention); anonymized training copies; CMEK encryption; DSAR automation |
| Model drift | Performance degrades over time due to new vehicle models, camera changes, seasonal lighting | Continuous monitoring (ADR-0011); drift detection alerts (mAP drop >5%); scheduled retraining (quarterly); A/B testing for new versions |
| Explainability disputes | Customer contests bounding box accuracy or false damage flags | Store annotated photos with predictions; human review overrides logged; dispute evidence package (original + annotated + model metadata) |

## Conclusion

Adopt a staged approach for damage detection model architecture: begin with Vertex AI AutoML Vision Object Detection for rapid production deployment, and escalate to custom CNN (EfficientDet or YOLOv8) hosted on Vertex AI if accuracy, latency, or business KPIs are not met. All models must output explainable predictions (bounding boxes, confidence scores), integrate with MLOps governance (ADR-0009/0011), support human-in-the-loop workflows (ADR-0016), and comply with photo retention policies (ADR-0017). This approach balances speed-to-market, customer trust, operational efficiency, and long-term accuracy.
