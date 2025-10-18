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

## Conclusion
Vertex AI provides a unique combination of geospatial intelligence, GenAI orchestration, and managed AI lifecycle, making it the most effective and low-friction platform for this mobility solution.
