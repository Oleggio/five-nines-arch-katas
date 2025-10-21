# ADR-0012 - Hybrid AI for User-Personalized In-app Companionship

## Context

To attract new customers and retain existing ones, mobile application will be extended with chat-bot functionality that creates exclusive rental and ride related communication experience.
The chat bot will enable both free and button-guided communication and allow to:
- navigate across all mobile app functions;
- allow user ask questions or initiate a desired function (e.g. booking);
- ask about vehicles rental & pricing;
- search available vehicles by parameters and nearby;
- request support;
- check parking nearby, etc.

The challenge is to balance **natural conversational flow** with **transactional accuracy and cost efficiency**.  While pure GenAI can handle natural dialogue, it lacks deterministic control for operations (e.g., booking, payments). Conversely, rule-based or traditional ML systems lack flexibility and user empathy. In addition, all core functionality questions/requests can be predefined and programmed via if-else chat bot coding.   

The goal is to implement a **hybrid AI stack** that orchestrates:
- **GenAI models** (for conversation understanding, paraphrasing, context retention)  
- **ML models** (for personalization, intent ranking, recommendation)  
- **Rule-based or code logic** (for core functionality user actions and standard requests)

This architecture will be orchestrated through the **Model Control Plane (MoCoP)** to ensure governance, model routing, cost control, and traceability across models. See [ADR-0013](ADR-0013%20-%20Adoption%20of%20Managed%20SaaS%20Model%20MoCoP.md) for details.


## Decision

A **Hybrid AI Architecture** approach will be adopted for in-app user companionship, combining **GenAI**, **ML models**, and **Code/Rule-based logic** within the orchestration layer.

The system flow will be as follows:
1. **User input (chat/text/button)** → passes through **NLU layer (GenAI)** for intent detection.  
2. **Intent classifier (ML)** scores confidence and routes to either GenAI continuation, predefined function (e.g., booking API), or support.  
3. **Rule-based logic** navigates through core function and basic requests (e.g. book a vehicle, what is price for X?, show me the closest parking/charging station nearby).  
4. **Response generation**: Hybrid response combining retrieved data and GenAI-generated natural text.  
5. **Evaluation & feedback loop** using **Vertex AI Experiments + Model Evaluation** for dialogue quality and system KPIs.  

This design ensures **flexibility, scalability, and safe orchestration** for conversational interactions. 

## Key Differentiators
- **Hybrid Control Plane (MoCoP)**  
  Enables dynamic routing between GenAI, ML, and rule-based functions, balancing creativity with compliance.

- **EU Data Residency**  
  Deployed via **Vertex AI (GCP)** in EU regions ensuring GDPR and DPA alignment.

- **Traceable Conversation Flow**  
  Every request/response is logged and evaluated through **Vertex AI Pipelines + Experiments** for continuous improvement.

- **Personalized Recommendations**  
  ML models leverage behavioral and contextual data (e.g., ride history, preferences, feedback loops).

- **Cost Governance**  
  MoCoP and token routing minimize GenAI overuse, delegating structured tasks to deterministic functions.

## Alternatives Considered
- **Alternative 1 — Full GenAI Chatbot**  
  + Pros: Natural UX, fast to prototype.  
  - Cons: High cost, no deterministic control, compliance risks, unstable API behavior.  

- **Alternative 2 — Rule-Based Bot Only**  
  + Pros: Predictable and cheap to operate.  
  - Cons: Poor customer experience, limited functionality, low potential to boost sales.

- **Alternative 3 — ML-Based Intent Router without GenAI**  
  + Pros: Efficient for short queries.  
  - Cons: Unable to handle unstructured or open-ended dialogue.

## Risks & Trade-offs
| Risk Area | Description | Mitigation |
|:--:|:--:|:--:|
| **Cost Overrun** | Uncontrolled GenAI calls may increase operational cost | Implement routing rules and token budgeting via MoCoP |
| **Model Hallucination** | GenAI may generate incorrect info | Combine deterministic templates for transactional intents |
| **Latency** | Multi-stage orchestration may slow down response | Use async pipelines, caching, and Vertex AI endpoints optimized for low latency |
| **Compliance** | Storing chat data may breach GDPR | Ensure EU data storage and anonymization |
| **UX Consistency** | Hybrid responses might vary in tone | Standardize tone templates and evaluation metrics in Vertex AI Evaluation |

## Conclusion
The **Hybrid AI architecture** allows the app to combine the **empathy and flexibility of GenAI** with the **reliability and predictability of ML and rule-based systems**. This design provides a **balanced, scalable, and compliant** conversational experience for users, while maintaining control over cost and safety through **Model Control Plane (MoCoP)** orchestration.
