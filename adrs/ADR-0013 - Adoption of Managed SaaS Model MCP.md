# ADR-0013 — Adoption of Managed SaaS Model Control Plane (MoCoP)

## Context
Our AI orchestration layer needs a **Model Control Plane (MoCoP)** to manage prompt routing, model selection, and policy enforcement.  
Given the project goals — **rapid enablement**, **minimal ops load**, and **EU data compliance** — we must choose between self-managed, cloud-native, or managed SaaS MoCoP.

We aim to:
- Orchestrate multiple GenAI and ML workloads for some of the applicable scenarios (see [FRs](/requirements/2_FRs.md)).  
- Ensure **GDPR-compliant EU data residency**.  
- Provide **governance, traceability, and evaluation** out-of-the-box.  
- Achieve the **fastest time to value** with minimal maintenance.

## Decision
Adopt a **Managed SaaS MoCoP** offering out-of-the-box orchestration, policy, safety, and evaluation layers with verified **EU data residency** and **DPA/SCC compliance**.

The MoCoP will serve as a central **AI gateway** handling:
- Prompt orchestration and policy injection.  
- Model routing and safety filtering.  
- Observability, evaluation, and rollback.  
- GDPR-aligned data handling and audit logging.

## Key Differentiators
- **Fastest Time to Value**  
  Integration within weeks, minimal platform setup.

- **Governance & Observability**  
  Built-in tracing, evaluation, and redaction without extra infra.

- **Multi-Model Routing**  
  Dynamic routing across providers for cost and quality balance.

- **EU Compliance**  
  Data processed in EU zones, vendor DPA and SCC coverage.

- **Predictable Costs**  
  Subscription or usage-based pricing with no infra overhead.

## Alternatives Considered
- **Self-Hosted MoCoP** — Full control but high ops effort and compliance cost.  
- **Cloud-Native MoCoP (Vertex AI, Bedrock, Azure AI)** — Tight cloud integration but limited EU control and multi-model flexibility.  
- **Custom Build** — Max flexibility, but requires dedicated platform engineering and governance build-out.

## Risks & Trade-offs

| Risk | Description | Mitigation |
|--|--|--|
| Vendor Lock-In | Dependence on one SaaS provider | Keep abstraction layer; choose open APIs |
| Data Residency | Risk of model routing outside EU | Verify EU-only in DPA; audit regularly |
| Cost Growth | Usage fees may rise with traffic | Set quotas; apply caching; budget alerts |
| Limited Control | SaaS policies may restrict tuning | Use vendor SDKs or extensions |
| Latency | Extra hop adds small delay | Define P95 targets; monitor via tracing |

## Conclusion
A **Managed SaaS MoCoP** offers the best balance of **speed, compliance, and reliability**. This path minimizes operational overhead and enables faster AI product delivery.  
Reassess after **12 months** to evaluate potential transition to **hybrid or self-managed** control for greater customization.
