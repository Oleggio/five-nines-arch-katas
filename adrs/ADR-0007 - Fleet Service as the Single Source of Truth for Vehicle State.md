# ADR-0007 — Fleet Service as the Single Source of Truth for Vehicle State

## Context

The platform requires a reliable, real-time, and authoritative view of vehicle state (location, charge, availability) to support routing, booking, maintenance, and operational decision-making.  

Telemetry is ingested through a clustered MQTT gateway and published to Pub/Sub. To avoid state fragmentation across services (ride management, maintenance, analytics), a central service must consolidate and manage the latest known state of each device.

This design aligns with the GCP IoT reference architecture, where the device registry and rules engine serve as the central authoritative component for device metadata, state, and event triggers.

---

## Decision

Use Fleet Service as the single source of truth for all vehicle state.  
Fleet Service:
- Receives telemetry from the MQTT gateway and Pub/Sub.  
- Updates and persists real-time state in the Fleet Store (e.g., Firestore).  
- Exposes APIs and publishes events for downstream consumers.  
- Implements configurable rules (e.g., charge threshold, location geofencing) to trigger operational actions.  

This service acts as both the device registry equivalent (authoritative state) and rules engine (decision point) in our architecture.

---

## Key Differentiators

- **Centralized state** — one canonical place for vehicle location, charge level, and availability.  
- **Rules engine integration** — battery threshold and operational triggers evaluated in one place.  
- **Tight integration with connectivity layer** — state updated directly from real-time telemetry.  
- **Low-latency updates** — near real-time propagation of state to ride and maintenance modules.  
- **Consistent downstream view** — avoids multiple services calculating their own state independently.

---

## Consideration_1 — Combining Connectivity and Core Business Logic

Traditional IoT architectures separate the connectivity layer (e.g., MQTT broker + Pub/Sub) from business logic. Here, Fleet Service combines device state managemen* with business rules evaluation (e.g., flagging low battery, marking vehicles unavailable, triggering maintenance events).  

This reduces latency, simplifies system topology, and ensures that operational decisions are always made based on the latest authoritative state.  
The approach mirrors GCP’s IoT platform pattern where device registry stores state and rules engine evaluates triggers.

---

## Alternatives Considered

- **Alternative 1 — Distributed state across multiple services**  
  Each module (ride, maintenance, analytics) maintains its own partial state, consuming telemetry directly from Pub/Sub.  
  *Cons:* risk of inconsistent state, duplicated logic, complex reconciliation.

- **Alternative 2 — Use Pub/Sub only as the state propagation mechanism**  
  All consumers read telemetry directly.  
  *Cons:* no central decision point, increased complexity of operational rules, higher coupling.

- **Alternative 3 — Dedicated IoT platform or registry component**  
  Introduce an additional device registry service separate from Fleet Service.  
  *Cons:* increased operational overhead, more moving parts, duplication of domain logic.

---

## Risks & Trade-offs

| Risk Area                     | Description                                                                                          | Mitigation                                                                                                 |
|-------------------------------|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| Single point of dependency     | Fleet Service becomes critical for most platform functions.                                          | Deploy with HA and autoscaling; implement robust observability and failover mechanisms.                     |
| Scalability                    | Centralizing state may create performance bottlenecks.                                               | Use scalable backend (e.g., Firestore / Bigtable), partition by region, and tune Pub/Sub ingestion.         |
| Operational complexity         | Combining rules and state may increase service complexity.                                          | Keep rule engine modular and configurable; separate business rules from ingestion logic internally.         |
| Vendor lock-in                 | Dependence on GCP services (Firestore, Pub/Sub) may impact portability.                             | Abstract storage and event layers; document migration paths.                                               |
| Latency impact                 | High message volumes could increase update latency.                                                 | Use asynchronous processing with backpressure handling and autoscaling of ingestion pipeline.              |

---

## Conclusion

Using Fleet Service as the single source of truth for vehicle state aligns with modern IoT platform architecture principles — combining device registry and rules engine responsibilities.  

This design ensures consistent, low-latency state across all modules, simplifies operational decision-making, and allows the system to scale predictably. While it introduces some operational concentration risk, this can be mitigated through high availability, observability, and modular rule handling
