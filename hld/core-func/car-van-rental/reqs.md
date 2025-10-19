## Detailed Functional Requirements​
Each requirement includes description, detailed specs, and business rules:
  - Advanced booking (7-day advance, fixed duration)
  - Booking eligibility verification (sufficient charge for requested duration)
  - Reservation management with extensions (conflict checking, approval workflow)
  - Vehicle access control (NFC + remote unlock API)
  - Return verification (parking bay + EV charger connection + AI photo proof)
  - Cross-border usage with origin city return constraint
  - Payment processing (per-minute, fines, pre-authorization with hold/release)
  - Real-time telemetry tracking (GPS, charge, range, 30-second intervals)
  - Issue reporting and roadside assistance (breakdown, charge depletion)
  - Partner charging location integration
  - Vehicle status lifecycle management (including remote disable capability)
  - Demand forecasting integration with event publishing

## Component Architecture​
Following microservices and bounded context principles:
  - Booking Service: Availability queries, reservation creation, charge sufficiency validation
  - Rental Lifecycle Service: Unlock, extend (with conflict checking), return workflows
  - Telemetry Ingestion Service: High-volume GPS/sensor data processing, range monitoring
  - Payment Calculation Service: Per-minute billing + fines, pre-authorization hold/release
  - Vehicle Status Manager: Operational status tracking, remote disable capability
  - Partner Charging Integration: Third-party charging location support, payment coordination
  - Roadside Assistance Dispatcher: Breakdown and charge depletion incident management
  - Demand Forecasting Integration: AI data exchange, event publishing for utilization patterns

Each component includes interfaces (REST APIs), internal modules, data stores, and dependencies .

## Clear Bounded Context Definition​
Using Domain-Driven Design terminology:
  - Ubiquitous language for car/van domain
  - Ownership boundaries (what this context controls)
  - Dependencies on external contexts (Customer, Payment, Fleet)
  - Domain events published and consumed
  - Clear separation from bike/scooter rental context

## Core Data Models​
JSON schemas for:
  - Reservation (booking details, pre-auth)
  - Rental (active usage session)
  - Vehicle Status (availability, location, charge)
  - Payment Record (charges, fines, audit trail)
  - Telemetry Record (time-series GPS/sensor data)

## Key ADRs to Create​
Critical architectural decisions with trade-offs:
  - PostgreSQL for reservation consistency (zero double-booking)
  - TimescaleDB for telemetry time-series data
  - Synchronous vehicle unlock vs async messaging
  - AI vs manual photo verification
  - Payment gateway vendor strategy (Stripe with portability)
  - Demand forecasting integration (batch + streaming)
  - Multi-country data sovereignty approach

## Integration Points​
Upstream dependencies (services you consume):
  - Customer Management, Payment Gateway, Fleet Management
  - Vehicle IoT Gateway, Notification Service, Photo Storage
  - Mapping Service (Google Maps/HERE)

Downstream consumers (who uses your APIs):
  - AI Demand Forecasting, Mobile App, Admin Dashboard, Crew Operations 