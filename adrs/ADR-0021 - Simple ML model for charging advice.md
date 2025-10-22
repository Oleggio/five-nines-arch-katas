# ADR-0021 — Simple ML model for charging advice to user

## Context
For electric vehicles (scooters, cars, e-bikes) used in the rental platform, maintaining sufficient battery levels throughout the trip is essential for a reliable customer experience.  

Functional Requirement **FR#2D** specifies:
> “Charging advice to user — triggered during route calculation or from battery level during riding, based on GPS tracker info, battery sensor info, and map data. Output: text recommendation to change route, park, or charge on suggested location(s). Tech: Code, ML.”  

The **Appendix B** explanation clarifies:
> “Based on the rented vehicle's battery state and its current location as well as map info, push-notification or voiced recommendation: change route, park or charge on a suggested location(s). Triggered during route calculation or just from battery level during riding.”  

Currently, users can see battery level but not proactive recommendations. Manual logic (simple thresholds) ignores context such as distance to next station, elevation, or predicted consumption rate.  A lightweight ML model can learn from historical ride and sensor data to generate timely and relevant **charging suggestions**, while remaining low-cost and easily deployable on mobile or backend systems.

## Decision

Adopt a **simple ML model** to provide real-time, context-aware charging advice to users.  

The model will:
- Take as input **current battery level**, **GPS position**, **map slope** and **destination distance**, and **charging-station/parking lot availability**.  
- Predict the **probability of battery depletion before destination** and output a recommendation _only if battery is too low_:  
  - “Choose a shorter route due to battery level.”
  - "Your battery is too low. Closest parking station is Z. Alternatively, closest charging point is X."  
  - “Choose another vehicle for this route. Suggested vehicle as at location Y.”  
- Run via low-latency backend inference.
- Prevent starting a ride with too low battery level and initiate double-confirmation in mobile app.  

The recommendation will be delivered via the **chatbot UI or push notification**, integrated with user dialogue components (FR#2A – 2E).
Chat bot conversation will be applied during route planning of the user. Push notification and/or voice-over will be used during ride.

## Key Differentiators

- **Consideration_1 — Lightweight Predictive Model, No GenAI**  
  Uses regression or shallow neural model, ensuring quick inference and minimal cloud cost while retaining sufficient prediction accuracy.

- **Consideration_2 — Context-Aware Decisioning**  
  Combines battery telemetry, route distance, map slope, parking lot and charging station availability to provide more relevant advice than static thresholds.

- **Consideration_3 — Seamless Integration with Route Guidance**  
  Works with adaptive route calculation (FR#2B) so that charging points can be included dynamically in the proposed route.

- **Consideration_4 — Scalable & Privacy-Preserving Design**  
  Only anonymized, aggregated trip data is used for model training; live inference uses on-device or EU-region backend endpoints.

## Alternatives Considered

- **Alternative_1: Rule-Based Threshold System**  
  Simple to build but not adaptive — ignores dynamic map parameters, parking and station availability, and consumption variability.
   
- **Alternative_2: Complex Deep Learning Model**  
  Provides slightly better accuracy but unnecessary operational complexity for this use case. Too expensive.
  
- **Alternative_3: GenAI Explanation Layer**  
  Adds interpretability but not required here; the advice can be expressed through predefined templates.

## Risks & Trade-offs

| **Data Quality** | Battery sensors and GPS trackers may provide incomplete readings | Implement validation logic; fallback to rule-based estimate if missing data |
| **Latency** | Real-time advice required during movement, while IoT messaging is every 30 min only | Use cached map data and precomputed routes; deploy lightweight model and use battery levels forecasts in-between the input |
| **Coverage of Charging Stations** | Outdated or incomplete station data leads to poor advice | Integrate with frequently updated map APIs and periodic data sync jobs |
| **Model Accuracy** | Limited initial dataset may affect early predictions | Use incremental learning from collected telemetry and user feedback as well as own history of battery maintenance and replacement |
| **User Trust** | Incorrect notifications will mislead users and reduce sales | Add confidence scoring and trigger above certain threshold, ensure proper model training per model and battery type |

## Conclusion
Implementing a **simple ML-based charging-advice model** offers a cost-effective way to enhance user safety and satisfaction without introducing unnecessary system complexity.  
This approach supports predictive energy management, integrates naturally with existing route and user notification functionality (push, voice-over), and ensures scalable performance suitable for large, real-time rental fleets.
