# ADR-0014 - GenAI Model Management on GCP

## Context
Model management includes registering, deploying, versioning, and governing foundation models. While Azure is an option, Vertex AI provides a fully managed model registry and deployment environment integrated with other AI components.

## Decision
Use Vertex AI Model Registry and Pipelines for GenAI model lifecycle management.

## Key Differentiators
- Native model registry with versioning.
- Integrated CI/CD for model deployment.
- Tight integration with embeddings, semantic search, and orchestration.

## Consideration_1 - End-to-End MLOps
Centralizing model management on Vertex AI ensures consistent governance and accelerates deployment.

## Alternatives Considered
- Azure AI Foundry: strong enterprise features, but disconnected from GCP stack.
- Databricks MLflow: flexible, but more setup effort.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|-----------|-------------|------------|
| Vendor lock-in | Deep integration with Vertex AI | Keep model artifacts portable |
| Cost | Managed features can be expensive | Usage controls and monitoring |
| Team skills | Vertex AI expertise required | Training and documentation |

## Conclusion
Vertex AI model management provides a unified and governed platform for managing GenAI models at scale.
