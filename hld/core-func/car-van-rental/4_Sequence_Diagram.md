# Car/Van Rental - Key Sequence Diagrams

## SD-001: Booking Flow
```mermaid
sequenceDiagram
    actor Customer
    participant App
    participant API Gateway
    participant Booking Service
    participant Payment Gateway
    participant Fleet Management
    participant Event Bus

    Customer->>App: Search availability
    App->>API Gateway: GET /api/vehicles/available
    API Gateway->>Fleet Management: Query fleet
    Fleet Management-->>API Gateway: Available vehicles
    API Gateway-->>App: Vehicle list
    
    Customer->>App: Book Tesla Model 3
    App->>API Gateway: POST /api/reservations
    API Gateway->>Booking Service: CreateReservation(customerId, vehicleId, slot)
    
    Booking Service->>Payment Gateway: Create pre-auth hold
    Payment Gateway-->>Booking Service: Pre-auth success
    
    Booking Service->>Booking Service: Insert reservation (DB transaction)
    Booking Service->>Event Bus: Publish ReservationCreated
    
    Booking Service-->>API Gateway: Reservation ID
    API Gateway-->>App: Booking confirmed
    App-->>Customer: Confirmation email
```

## SD-002: Unlock & Start Rental
[Detailed sequence showing NFC/app unlock → rental session creation → telemetry start]

## SD-003: Return & Payment Capture
[Sequence showing photo upload → AI verification → payment finalization → fine calculation]

[Add 5-7 critical flows...]