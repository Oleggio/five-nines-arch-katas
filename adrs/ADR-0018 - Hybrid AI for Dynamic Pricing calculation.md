# ADR-0018 — Hybrid AI for Dynamic Pricing calculation

## Context

The rental platform requires **real-time, adaptive pricing** for vehicles (scooters, cars, vans, e-bikes) to reflect changing market conditions and offer attractive, competitive pricing for the rental services provided.  

Traditional static pricing or rule-based engines fail to capture **dynamic factors** such as:
- current demand at location, weather, local events, etc.
- competitor prices
- user rental patterns
- vehicle availability per type/location

To stay competitive and optimize both **profit margin** and **customer satisfaction**, pricing must adjust dynamically using both **AI-driven insights** and **deterministic business logic**.
In addition, Per [Appendix B: AI scenarios explained](/requirements/Appendix%20B%3A%20AI%20scenarios%20explained.md), this AI functionality output will be used by in-app companionship (see [ADR-0012](ADR-0012%20-%20Hybrid%20AI%20for%20User-personalized%20in-app%20companionship.md)) which means it should be as user-personalized as in-app communication is.

Functional Requirement **FR#2F** defines:
> “Real-time price calculation (back end): dynamically calculated price of vehicle rental for a certain user based on vehicle type, rental params, weather, user params, previous user rental patterns, competitor prices, location, etc.”  

Per [Appendix B: AI scenarios explained], this AI functionality supports not only backend pricing calculation but also **front-end pricing advice** to users (FR#2C), enabling chat-based interaction to explain or compare pricing options.

Thus, a **Hybrid AI** approach combining Machine Learning, GenAI, and rule-based code is required to deliver transparent, adaptive, and explainable pricing results in real time.

## Decision

Adopt a **Hybrid AI approach** for the Dynamic Pricing engine that combines:
- **ML Models**: Predict price elasticity and optimize base price dynamically.
- **GenAI Layer**: Explain pricing options and contextual factors to users in natural language (e.g., “price is higher due to city event and demand increase”).
- **Rule-Based Core Logic**: Apply hard constraints and business policies (minimum margin, local regulations, subscription discounts).

The system will be orchestrated through the **Model Control Plane (MoCoP)** for traceability, versioning, and model governance, ensuring **GDPR-compliant EU data residency** ([NFR_7](/requirements/3_NFRs.md)) and consistent model evaluation ([ADR-0010](/adrs/ADR-0010%20-%20Evaluation%20and%20Adoption%20of%20Vertex%20AI%20Agent%20Builder%20for%20AI%20Service%20and%20Orchestration.md)).

## Key Differentiators

- **Consideration_1 — Hybrid Approach for Accuracy + Explainability**  
  ML ensures optimized price precision while rules ensure business constraints. GenAI layer adds user-facing explainability to promote trust.

- **Consideration_2 — Continuous Learning from Market Signals**  
  Models are retrained with event, demand, and competitor data to refine predictions for each region.

- **Consideration_3 — Seamless Integration with User Dialogue System**  
  Enables real-time price advice ([FR#2C](/requirements/2_FRs.md)) within chatbot UI to promote upsell or subscription conversions.

- **Consideration_4 — Compliance & Governance via MoCoP**  
  Provides controlled routing between GenAI and ML layers, logging all inference events for auditing.

## Alternatives Considered
- **Alternative_1: Pure Rule-Based Pricing**  
  Easy to implement but lacks adaptability and misses optimization opportunities from dynamic conditions.
- **Alternative_2: ML-Only Model**  
  Could produce optimized results but may fail under corner cases (e.g., local laws, minimum fare) without deterministic rules.
- **Alternative_3: GenAI-Only Approach**  
  Provides natural interaction but lacks reliability for numerical precision and may introduce compliance risks.

---

## Risks & Trade-offs

| Risk Area | Description | Mitigation |
|--|--|--|
| **Model Drift** | Market patterns change seasonally, leading to inaccurate price recommendations. | Implement continuous retraining and model evaluation pipeline via Vertex AI. |
| **Latency** | Real-time pricing requires low-latency computation. | Cache frequent routes and apply tiered evaluation (ML prediction first, GenAI optional). |
| **Explainability** | ML predictions may be hard to justify to end-users or regulators. | Use GenAI layer to provide reasoned explanations from structured inference logs. |
| **Cost** | Running GenAI models for all requests can increase expenses. | Restrict GenAI to user-facing explanation layer; backend pricing remains ML + rules. |
| **Data Compliance** | Use of external APIs for competitor data may expose PII or location data. | Anonymize data and host all processing within EU-compliant cloud (Vertex AI EU region). |

---

## Conclusion
The **Hybrid AI** design for dynamic pricing aligns with business objectives of competitiveness, transparency, and scalability. It allows flexible orchestration between ML prediction, GenAI explanation, and rule-based enforcement as well as delivering **smart, compliant, and explainable pricing** adaptable to evolving market signals.
