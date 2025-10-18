# Car/Van Rental - Data Models

## Reservation Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "reservationId": { "type": "string", "format": "uuid" },
    "customerId": { "type": "string", "format": "uuid" },
    "vehicleId": { "type": "string" },
    "vehicleType": { "enum": ["car", "van"] },
    "startTime": { "type": "string", "format": "date-time" },
    "endTime": { "type": "string", "format": "date-time" },
    "pickupLocation": {
      "type": "object",
      "properties": {
        "lat": { "type": "number" },
        "lng": { "type": "number" },
        "address": { "type": "string" }
      }
    },
    "preAuthAmount": { "type": "number", "minimum": 0 },
    "preAuthId": { "type": "string" },
    "status": { 
      "enum": ["pending", "confirmed", "active", "completed", "cancelled"] 
    },
    "createdAt": { "type": "string", "format": "date-time" }
  },
  "required": ["reservationId", "customerId", "vehicleId", "startTime", "endTime"]
}
```

## Rental Session Schema
[Similar JSON schema for active rental...]

## Telemetry Record Schema
[Time-series data schema...]

## Payment Record Schema
[Billing + fines schema...]

## Database Schema (PostgreSQL)

```sql
CREATE TABLE reservations (
        reservation_id UUID PRIMARY KEY,    
    customer_id UUID NOT NULL,    
    vehicle_id VARCHAR(50) NOT NULL,    
    vehicle_type VARCHAR(10) CHECK (vehicle_type IN ('car', 'van')),    
    start_time TIMESTAMPTZ NOT NULL,    
    end_time TIMESTAMPTZ NOT NULL,    
    pickup_location JSONB,    
    pre_auth_amount DECIMAL(10,2),    
    pre_auth_id VARCHAR(100),    s
    tatus VARCHAR(20),    
    created_at TIMESTAMPTZ DEFAULT NOW(),  
    -- Prevent double-booking    
    CONSTRAINT no_overlap EXCLUDE USING gist (        
        vehicle_id WITH =,        
        tstzrange(start_time, end_time) WITH &&    ));
    CREATE INDEX idx_reservations_customer ON reservations(customer_id);CREATE INDEX idx_reservations_vehicle_time ON reservations(vehicle_id, start_time);
```

[Add indexes, constraints, TimescaleDB hypertable config...]