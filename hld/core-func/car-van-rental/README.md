# Car/Van Rental - High-Level Design

## Overview

This folder contains the architectural documentation for the **Car/Van Rental** bounded context, a critical subset of the broader MobilityCorp vehicle rental business.

## Why Separate from Other Vehicle Types?

Car/van rentals have distinct characteristics that warrant separate architectural modeling:

- **Advance bookings** (up to 7 days) vs scooters/bikes (30 minutes)
- **Longer rental durations** (hours to days) vs micro-mobility (minutes)
- **Charging infrastructure** requirements (dedicated bays, partner networks)
- **Higher transaction values** (€15-50/hour) requiring stricter payment controls
- **Complex return verification** (4 photos, charger validation) vs simple parking
- **Corporate account support** with bulk billing and usage caps
- **Recurring reservations** for commuter use cases

While the overall platform serves all vehicle types, cars and vans share enough common patterns (booking, damage detection, fleet management) to be modeled as a single bounded context, distinct from lightweight micro-mobility services.

## Contents

| File | Purpose |
|------|---------|
| **1_Context_Diagram.md** | System boundaries, external dependencies, actors |
| **2_Container_Diagram.md** | Services, databases, AI integrations, technology stack |
| **3_Domain_Model.md** | Aggregates, domain events, consistency boundaries |
| **4_Sequence_Diagram.md** | Critical flows: booking, unlock, return, AI verification, disputes |
| **diagrams/** | Draw.io source files for context and container diagrams |

## Key Architectural Patterns

- **Event-driven architecture** via Pub/Sub for asynchronous workflows
- **AI-first approach** for cleanliness and damage detection (Vertex AI)
- **Human-in-the-loop** fallback for AI confidence thresholds
- **Microservices** with clear aggregate boundaries (DDD)
- **Multi-tenant B2B support** with corporate account isolation

## Related Documentation

- **Functional Requirements:** `/requirements/Appendix A: Core functionality.md`
- **Non-Functional Requirements:** `/requirements/3_NFRs.md`
- **ADRs:** `/adrs/` - Architecture decision records (e.g., ADR-0003 Vertex AI, ADR-0007 Fleet Service)
- **ML/AI Scenarios:** `/hld/scenarios/` - Detailed AI model architectures
