# ADR-0003 — Vertex AI as core platform for AI and GenAI

## Context
The solution requires an AI/GenAI platform capable of:  
- Real-time route optimization and demand forecasting for connected vehicles.  
- Integration with geospatial data (POIs, routes, traffic).  
- Supporting generative agents and LLM-based workflows for operational decision-making, automation, and conversational interfaces.  
- Minimal infrastructure overhead and tight integration with the data backbone.

## Decision
Use Google's VertexAI as the core platform for model training, serving, orchestration, and GenAI agent workflows.

## Key Differentiators
- **Native integration with Google Maps**, giving unique geospatial context for models (routing, POI intelligence, ETA predictions).  
- **E2E managed ML/GenAI stack:** data prep, training, tuning, deployment, orchestration (pipelines), and prompt management.  
- **Agentic workflow support:** native orchestration with function calling, tool integration, and prompt chaining.

## Consideration_1 — Accelerated Mobility Intelligence
Vertex AI enables models to consume real-time telemetry and Maps data directly, supporting predictive and prescriptive use cases (e.g., rerouting, demand heatmaps, anomaly detection). Competing platforms require additional integrations or third-party APIs.

## Alternatives Considered
- **AWS Sagemaker + external mapping providers:** adds complexity, licensing cost, and latency.  
- **Azure AI Foundry + Azure Maps** lacks direct mapping integration, requires custom orchestration for GenAI workflows.  
- **Custom open-source stack:** more flexibility, but high operational overhead and slower time-to-market.

## Risks & Trade-offs
| Risk Area | Description | Mitigation |
|--|--|--|
| Vendor lock-in                   | Heavy reliance on Vertex AI’s managed stack and Maps integration may make migration costly or complex.             | Keep model artifacts portable; design APIs and orchestration to allow future abstraction or hybrid setups.  |
| Cost control                     | Managed training, inference, and GenAI workloads can scale unpredictably with increased fleet size or usage.       | Implement budget alerts, quota controls, and usage dashboards; prioritize efficient model architectures.    |
| Feature dependency               | Tight coupling to Vertex AI features (function calling, prompt orchestration, model hosting) can limit flexibility. | Use modular workflows where possible; document fallback or alternative service strategies.                   |
| Model governance & compliance    | Using a managed GenAI platform introduces dependency on vendor data handling policies and governance frameworks.   | Define clear data governance boundaries; use private endpoints and explicit data retention settings.         |
| Latency for critical operations  | GenAI workflows can introduce additional latency compared to pure predictive models.                               | Use hybrid approach: combine deterministic routing models with GenAI orchestration for conversational logic. |
| Security & access control        | Broader AI service surface increases attack vectors and misconfiguration risks.                                    | Enforce strict IAM, secret management, and service isolation; follow least-privilege principles.             |
| Skills & adoption                | Vertex AI GenAI features may be less familiar to the current engineering team.                                     | Provide training, reference architectures, and pre-built templates.                                          |
| Ecosystem flexibility            | Integration with non-Google services may be limited or require custom adapters.                                    | Standardize external tool integration through well-defined APIs and service gateways.                        |

## Conclusion
Vertex AI provides a unique combination of geospatial intelligence, GenAI orchestration, and managed AI lifecycle, making it the most effective and low-friction platform for this mobility solution.
