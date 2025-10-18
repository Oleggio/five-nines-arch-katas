# Car/Van Rental - System Context Diagram

## Purpose
Shows how the Car/Van Rental system fits into the broader MobilityCorp ecosystem.

## Diagram
```mermaid
C4Context
    title System Context - Car/Van Rental

    Person(customer, "Customer", "Rents cars/vans via mobile app")
    Person(crew, "Operations Crew", "Manages fleet and resolves issues")
    
    System_Boundary(rental, "Car/Van Rental System") {
        System(carvan, "Car/Van Rental", "Handles reservations, payments, tracking")
    }
    
    System_Ext(customer_mgmt, "Customer Management", "Identity & KYC")
    System_Ext(payment, "Payment Gateway", "Stripe")
    System_Ext(fleet, "Fleet Management", "Vehicle inventory")
    System_Ext(iot, "Vehicle IoT", "Telemetry & access control")
    System_Ext(ai_forecasting, "AI Demand Forecasting", "Predictive analytics")
    System_Ext(maps, "Google Maps", "Routing & geocoding")
    
    Rel(customer, carvan, "Books & manages rentals")
    Rel(crew, carvan, "Monitors & supports operations")
    
    Rel(carvan, customer_mgmt, "Verifies identity")
    Rel(carvan, payment, "Processes payments")
    Rel(carvan, fleet, "Checks availability")
    Rel(carvan, iot, "Unlocks & tracks vehicles")
    Rel(carvan, maps, "Geocodes locations")
    Rel(ai_forecasting, carvan, "Consumes booking data")
```

## Key Insights
- Single Responsibility: This context ONLY handles car/van rentals (bikes/scooters separate)
- External Dependencies: 6 upstream systems (see Integration Requirements)
- Downstream Consumers: AI Forecasting, Mobile App, Admin Dashboard

