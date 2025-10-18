
# Car/Van Rental - Container Diagram

## Purpose
Shows the internal containers (services, databases) within the Car/Van Rental system.

## Diagram
```mermaid
C4Container
    title Container Diagram - Car/Van Rental System

    Person(customer, "Customer")
    Person(crew, "Operations Crew")
    
    System_Boundary(rental, "Car/Van Rental System") {
        Container(api_gateway, "API Gateway", "Cloud Run", "Routes requests, auth")
        Container(booking_svc, "Booking Service", "Cloud Run", "Reservation logic")
        Container(rental_svc, "Rental Lifecycle Service", "Cloud Run", "Unlock, extend, return")
        Container(telemetry_svc, "Telemetry Ingestion", "Dataflow", "Processes GPS/sensor data")
        Container(payment_calc, "Payment Calculator", "Cloud Run", "Billing & fines")
        
        ContainerDb(postgres, "Reservation DB", "PostgreSQL", "Bookings & rentals")
        ContainerDb(timescale, "Telemetry DB", "TimescaleDB", "Time-series vehicle data")
        ContainerDb(cache, "Session Cache", "Redis", "Active rental state")
        
        Container(event_bus, "Event Bus", "Pub/Sub", "Domain events")
    }
    
    System_Ext(payment_gw, "Payment Gateway")
    System_Ext(iot, "Vehicle IoT")
    
    Rel(customer, api_gateway, "HTTPS")
    Rel(api_gateway, booking_svc, "gRPC")
    Rel(api_gateway, rental_svc, "gRPC")
    Rel(booking_svc, postgres, "SQL")
    Rel(rental_svc, postgres, "SQL")
    Rel(rental_svc, cache, "Redis protocol")
    Rel(iot, telemetry_svc, "MQTT → Pub/Sub")
    Rel(telemetry_svc, timescale, "SQL")
    Rel(payment_calc, payment_gw, "HTTPS")
    Rel(booking_svc, event_bus, "Publishes ReservationCreated")
    Rel(rental_svc, event_bus, "Publishes RentalCompleted")
```

## Container Responsibilities
Booking Service
  - Purpose: Manage reservations
  - Tech Stack: Go, gRPC
  - Data Store: PostgreSQL (ACID compliance)
  - Related ADR: ADR-0001 (PostgreSQL choice)

Rental Lifecycle Service
  - Purpose: Handle unlock → track → return flow
  - Tech Stack: Node.js, REST + gRPC
  - Data Store: PostgreSQL + Redis (session state)
  - Related ADR: ADR-0003 (Sync unlock decision)

Telemetry Ingestion Service
  - Purpose: Process high-volume GPS/sensor streams
  - Tech Stack: Apache Beam (Dataflow)
  - Data Store: TimescaleDB
  - Related ADR: ADR-0002 (TimescaleDB choice)
  
[Continue for each container...]