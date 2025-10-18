# Car/Van Rental - Component Diagram (Booking Service)

## Purpose
Drill-down into Booking Service internal components.

## Diagram
[Mermaid diagram showing modules: AvailabilityChecker, ReservationCreator, PaymentAuthorizer, EventPublisher]

## Component Details

### AvailabilityChecker
**Responsibility:** Query Fleet Management for vehicle availability  
**Input:** Vehicle type, location, timeslot  
**Output:** List of available vehicles  
**Dependencies:** Fleet Management API, Geospatial cache

### ReservationCreator
**Responsibility:** Atomically create reservation with pre-auth  
**Input:** Customer ID, vehicle ID, timeslot  
**Output:** Reservation ID  
**Transaction Boundary:** Database transaction (ADR-0001)

[Continue for each component...]