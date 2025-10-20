# ADR-0011 - Model Evaluation Flow using Vertex AI

## Context

Our mobility platform uses AI and GenAI models for routing, demand forecasting, and conversational agents. To maintain model quality, reliability, and governance, we need a formal evaluation flow that ensures models are tested and monitored before and after deployment.  

The evaluation process must support:
- Predictive models (e.g., routing, ETA estimation)  
- Generative models (e.g., agent responses, recommendations)  
- Integration with MLOps pipelines, CI/CD, and versioning  
- Quantitative and qualitative metrics to guide deployment decisions

Vertex AI offers managed evaluation capabilities for both predictive and generative models, including support for adaptive rubrics, comparison workflows, and automated integration with pipelines.

---

## Decision

Adopt Vertex AI Model Evaluation and GenAI Evaluation Service as the core platform for model evaluation.  
Key steps in the evaluation flow:
- Prepare evaluation datasets (from ground truth or production logs)  
- Define appropriate metrics (standard, adaptive, and model-based)  
- Execute evaluations (pointwise and pairwise)  
- Integrate evaluation into CI/CD pipelines (Vertex AI Pipelines)  
- Use results for versioning, promotion decisions, retraining triggers, and monitoring

---

## Key Differentiators

- Single platform for predictive and generative model evaluation.  
- Built-in support for adaptive rubrics and model-based metrics for GenAI.  
- Tight integration with Vertex AI stack (model registry, pipelines, endpoints).  
- Native comparison workflows (baseline vs candidate).  
- Enables deployment gating and continuous evaluation at scale.

---

## Consideration_1 - Continuous Evaluation & Governance

Evaluation is not a one-time step but a continuous part of the MLOps lifecycle.  
- After training, models are evaluated before deployment.  
- Evaluation is repeated periodically in production to detect drift.  
- Metrics drive automated gating, retraining, and rollback strategies.  
- For GenAI, evaluation includes human-in-the-loop checks and adaptive scoring to ensure response quality and relevance.

This approach aligns with the GCP evaluation framework and our governance principles.

---

## Alternatives Considered

- **Custom evaluation framework (self-managed)**  
  Pros: full control, portable  
  Cons: high maintenance, more complexity to scale

- **MLflow-based evaluation stack**  
  Pros: open-source, flexible, widely adopted in MLOps workflows  
  Cons: requires custom integration with GenAI metrics, limited out-of-the-box support for LLM evaluation

- **Azure ML evaluation tools**  
  Pros: enterprise-grade tooling  
  Cons: integration complexity with GCP and our Vertex stack

- **Manual and ad-hoc evaluation**  
  Pros: low setup effort initially  
  Cons: poor scalability, governance gaps, increased drift risk

---

## Risks & Trade-offs

| Risk Area              | Description                                                                | Mitigation                                                                 |
|-------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------|
| Vendor lock-in          | Strong dependency on Vertex AI evaluation stack                            | Keep evaluation data exportable, design interfaces for portability           |
| Cost controls           | Evaluating large datasets or frequent model comparisons can increase cost | Sample evaluation intelligently, automate archiving, set quotas              |
| Latency to deployment   | Additional evaluation steps may extend release cycle                       | Automate evaluation pipeline and gating criteria                             |
| Skills                  | Team needs experience with adaptive rubrics and GenAI metrics              | Provide training and evaluation templates                                    |
| Over-automation         | Metrics alone may not reflect business quality                             | Combine metrics with human review for critical versions                       |

---

## Conclusion

Vertex AI evaluation services provide a scalable and integrated approach to model evaluation, supporting both predictive and generative use cases.  

They allow us to embed continuous evaluation directly into our MLOps lifecycle, improve governance, and make informed deployment decisions. A pilot will be executed to:
- Set up baseline evaluation pipelines for both predictive and generative models  
- Integrate with model versioning and CI/CD  
- Define adaptive rubrics and gating policies  
- Compare against a minimal MLflow-based evaluation workflow for fallback or hybrid scenarios

If successful, Vertex AI will be adopted as the primary evaluation platform, with MLflow as a secondary or complementary option.
