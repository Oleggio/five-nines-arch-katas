# ADR-0013 - Semantic Search on GCP

## Context
Semantic search is critical for GenAI use cases, enabling retrieval of relevant and context-aware information. While Azure Cognitive Search or Databricks could be used, leveraging GCP allows for tight integration with Vertex AI and BigQuery vector search.

## Decision
Use BigQuery vector search, Vertex AI embeddings, and optional Vertex AI Matching Engine for semantic search.

## Key Differentiators
- Built-in vector search and embedding support.
- High scalability and low latency.
- Seamless integration with Vertex AI and knowledge store.

## Consideration_1 - Native Vector Capabilities
Using GCP-native vector search reduces complexity and improves retrieval speed.

## Alternatives Considered
- Azure Cognitive Search: mature, but external to Vertex AI.
- Databricks + Milvus: flexible but more operational overhead.

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|-----------|-------------|------------|
| Vendor dependency | Tight coupling with GCP search stack | Use standard vector formats and RAG architecture |
| Scaling | High query loads | Capacity planning and autoscaling |
| Cost | Vector search can grow quickly with data | Monitoring and cost governance |

## Conclusion
BigQuery vector search with Vertex AI embeddings offers a powerful, integrated solution for semantic search with minimal operational overhead.
