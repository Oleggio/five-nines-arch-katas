# ADR-0010 - Evaluation and Adoption of Vertex AI Agent Builder for AI Service and Orchestration

## Context

The AI service in our platform is responsible for orchestrating agent logic, integrating GenAI capabilities, and enabling operational decision-making through natural language interactions. It must support:
- Real-time routing and operational recommendations
- Integration with external systems (Maps, Fleet APIs, knowledge store)
- Tool invocation and contextual memory
- Governance, observability, and evaluation of AI behavior

We currently use Vertex AI as our core GenAI platform. Vertex AI Agent Builder provides a managed agent runtime with orchestration, tool calling, memory, and evaluation capabilities out of the box.

---

## Decision

Adopt Vertex AI Agent Builder as the orchestration layer for GenAI-based services, starting with a proof of concept (PoC) to validate its performance, cost, and flexibility before full production rollout.

---

## Key Differentiators

- Fully managed agent runtime, reducing infrastructure overhead.
- Built-in tool invocation and multi-agent workflows.
- Native integration with Vertex AI, BigQuery, Maps data, and external APIs.
- Memory support (short-term and long-term) and RAG patterns.
- Integrated observability, evaluation, and quality monitoring of agent behavior.
- Faster time-to-market compared to building custom orchestration.

---

## Consideration_1 - Managed Agent Orchestration

Using Vertex AI Agent Builder allows us to consolidate orchestration, memory, and tool calling in a single managed component.  
This aligns with the overall GCP stack strategy and enables faster iteration on operational and user-facing GenAI capabilities without maintaining separate orchestration services.

---

## Alternatives Considered

- **Custom orchestration layer on Cloud Run or GKE**  
  More control but significantly more engineering and maintenance effort.

- **Azure OpenAI orchestration**  
  Feature-rich but requires cross-cloud integration and increased complexity.

- **Third-party agent frameworks (LangChain, LlamaIndex)**  
  Flexible but adds operational overhead and lacks integrated observability.

---

## Risks & Trade-offs

| Risk Area                  | Description                                                                                     | Mitigation                                                                                                   |
|----------------------------|-------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| Vendor lock-in             | Strong dependency on GCP agent runtime                                                          | Abstract business logic and tools; design for portability where feasible                                     |
| Feature maturity           | Some Agent Builder features are in preview and may evolve                                      | Use PoC to validate critical features before committing                                                      |
| Cost control               | High token and tool invocation volume can drive costs up                                       | Implement quotas, caching, and monitoring early                                                              |
| Latency                    | Orchestration layer may introduce additional response time                                    | Test end-to-end latency; use direct model calls for latency-sensitive scenarios                              |
| Skills                     | New platform capabilities require team upskilling                                             | Training program, internal documentation, reference implementations                                         |

---

## Conclusion

Vertex AI Agent Builder provides a strong foundation for AI service orchestration, integrating tool invocation, memory, and evaluation in a managed environment. This aligns with our GCP-centric architecture and reduces operational overhead compared to custom-built solutions.

PoC will be executed to:
- Implement a simple operational agent using Fleet and Maps data
- Measure end-to-end latency and token usage
- Validate cost and performance at small scale
- Evaluate fallback and resilience strategies

If PoC results meet defined targets, Agent Builder will be adopted as the primary orchestration layer for AI service components.
