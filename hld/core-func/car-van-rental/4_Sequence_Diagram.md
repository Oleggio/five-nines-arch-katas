# Car/Van Rental - Key Sequence Diagrams

> **Coverage:** These sequences represent critical paths through the car/van rental system, showing interactions between containers, external systems, and domain aggregates. NFR annotations highlight performance and reliability requirements.

---

## SD-001: Booking Flow (Happy Path)

**Functional Requirements:** FR#1-2 (Vehicle Search), FR#1-3 (Booking), FR#1-6 (Payment)  
**NFRs:** NFR_3 (Performance: Booking ≤2s), NFR_4 (Security: PCI DSS), NFR_1 (Scalability: 500 searches/sec peak)  
**ADRs:** ADR-0001 (GCP), ADR-0002 (Pub/Sub events)

```mermaid
sequenceDiagram
    actor Customer
    participant App as Mobile App
    participant Gateway as API Gateway
    participant Booking as Booking Service
    participant Fleet as Fleet Management
    participant Payment as Payment Gateway
    participant EventBus as Pub/Sub
    participant DB as PostgreSQL

    Note over Customer,DB: NFR_3: Total flow ≤2 seconds (p95)
    
    Customer->>App: Search "Tesla near me, 2pm-4pm"
    App->>Gateway: GET /api/vehicles/available?lat=52.37&lon=4.89&start=14:00
    Gateway->>Fleet: QueryAvailability(location, timeSlot)
    Fleet->>DB: SELECT vehicles WHERE available=true
    DB-->>Fleet: 12 vehicles (with charge %, location)
    Fleet-->>Gateway: [Tesla Model 3 (85%), VW ID.4 (72%), ...]
    Gateway-->>App: Vehicle list with pricing
    App-->>Customer: Show map with available cars
    
    Note over Customer,App: Customer selects Tesla Model 3
    Customer->>App: Book "Tesla Model 3" for 2h
    App->>Gateway: POST /api/reservations<br/>{vehicleId, customerId, slot, recurringPattern?}
    
    Gateway->>Booking: CreateReservation(request)
    
    Note over Booking,Payment: NFR_4: PCI DSS tokenization
    Booking->>Payment: CreatePreAuth(customerId, amount)
    Note over Payment: Hold = (2h × 1.5) × €15/h + €200 = €245
    Payment-->>Booking: PreAuth token, expiresAt
    
    Booking->>DB: BEGIN TRANSACTION
    Booking->>DB: INSERT reservation (vehicleId, customerId, preAuthToken)
    Booking->>DB: UPDATE vehicle SET status='reserved'
    Booking->>DB: COMMIT
    
    Note over Booking,EventBus: Event-driven availability update
    Booking->>EventBus: Publish ReservationCreated
    
    Booking-->>Gateway: {reservationId, confirmationCode, preAuthAmount}
    Gateway-->>App: Booking confirmed
    App-->>Customer: Confirmation + QR code for unlock
    
    Note over EventBus,Fleet: Eventual consistency
    EventBus-->>Fleet: ReservationCreated event
    Fleet->>DB: Update vehicle availability cache
```

**Key Decisions:**
- Pre-authorization created **before** reservation insert (fail-fast on payment issues)
- Vehicle availability update via event (eventual consistency acceptable for non-critical path)
- Confirmation QR code includes signed JWT for secure unlock (15-min expiry)

---

## SD-002: Unlock & Start Rental (NFC + Fallback)

**Functional Requirements:** FR#1-4 (Lock/Unlock), FR#1-5 (GPS Tracking)  
**NFRs:** NFR_3 (Performance: Unlock ≤3s p99), NFR_2 (Reliability: Circuit breakers)  
**ADRs:** ADR-0002 (Vehicle telemetry), ADR-0007 (Fleet Service as SSOT)

```mermaid
sequenceDiagram
    actor Customer
    participant App as Mobile App
    participant Gateway as API Gateway
    participant Rental as Rental Lifecycle Service
    participant Fleet as Fleet Service
    participant VehicleIoT as Vehicle IoT System
    participant EventBus as Pub/Sub
    participant DB as PostgreSQL

    Note over Customer,DB: NFR_3: NFC unlock ≤3 seconds (p99)
    
    Customer->>App: Tap "Unlock" (8:57am, reservation 9:00-11:00am)
    Note over App: Check unlock window: 15 min before to 30 min after start
    App->>App: Validate reservation time window
    
    alt NFC Available (Primary Path)
        Customer->>VehicleIoT: NFC tap (ISO 14443 protocol)
        VehicleIoT->>VehicleIoT: Verify signed token in NFC payload
        VehicleIoT-->>Customer: Door unlocked (haptic feedback)
        VehicleIoT->>EventBus: Publish VehicleUnlocked event
        Note over VehicleIoT: Total: 1.2s (p50), 2.8s (p99)
    else NFC Failed or Not Available (Fallback)
        App->>Gateway: POST /api/rentals/{reservationId}/unlock
        Gateway->>Rental: UnlockVehicle(reservationId, customerId, location)
        
        Rental->>Fleet: GetVehicleStatus(vehicleId)
        Fleet-->>Rental: {location, locked=true, charge=85%}
        
        Rental->>Rental: Validate customer at vehicle location (±50m GPS)
        
        Note over Rental,VehicleIoT: NFR_2: Circuit breaker pattern
        Rental->>VehicleIoT: SendUnlockCommand(vehicleId, token)
        VehicleIoT-->>Rental: Command accepted, unlocking...
        
        Rental-->>Gateway: Unlock initiated
        Gateway-->>App: "Unlocking... (3-5 seconds)"
        
        VehicleIoT->>EventBus: Publish VehicleUnlocked
        EventBus-->>App: Push notification "Vehicle unlocked"
    end
    
    Note over EventBus,Rental: Start rental session (event-driven)
    EventBus-->>Rental: VehicleUnlocked event
    
    Rental->>DB: BEGIN TRANSACTION
    Rental->>DB: INSERT rental_session<br/>(reservationId, startTime, startLocation, startOdometer)
    Rental->>DB: UPDATE reservation SET status='active'
    Rental->>DB: COMMIT
    
    Rental->>EventBus: Publish RentalStarted
    
    Note over Rental: Start telemetry stream
    Rental->>VehicleIoT: Subscribe to telemetry (30s intervals)
    
    loop Every 30 seconds
        VehicleIoT->>EventBus: TelemetryReceived<br/>{GPS, battery%, speed, odometer}
        EventBus-->>Rental: Process telemetry
        Rental->>DB: INSERT telemetry_record (time-series table)
    end
    
    App-->>Customer: "Rental started. Drive safely!"
```

**Key Decisions:**
- NFC primary method for best UX (1-3s unlock)
- Remote API fallback for devices without NFC or signal issues
- GPS validation (±50m) prevents unlock fraud
- Telemetry starts immediately after unlock (30s intervals)
- Circuit breaker on Vehicle IoT calls (fail gracefully if down)

---

## SD-003: Return & AI Photo Verification (Cleanliness + Damage)

**Functional Requirements:** FR#1-7 (Return Flow), FR#2L (Cleanliness), FR#2M (Damage Detection)  
**NFRs:** NFR_9 (AI: Confidence 90% cleanliness, 85% damage), NFR_3 (Performance: Photo analysis ≤5s)  
**ADRs:** ADR-0003 (Vertex AI), ADR-0014 (ML Model Selection), ADR-0016 (Human-in-the-Loop), ADR-0017 (Photo Retention 90d)

```mermaid
sequenceDiagram
    actor Customer
    participant App as Mobile App
    participant Gateway as API Gateway
    participant Rental as Rental Lifecycle Service
    participant PhotoService as AI Photo Analysis Service
    participant GCS as Cloud Storage (GCS)
    participant VertexAI as Vertex AI Vision
    participant Crew as Crew Dispatch Service
    participant Payment as Payment Service
    participant EventBus as Pub/Sub
    participant DB as PostgreSQL

    Note over Customer,DB: NFR_3 + NFR_9: Photo analysis ≤5s, confidence thresholds 90%/85%
    
    Customer->>App: Tap "End Rental" (location: valid parking bay)
    App->>App: Validate GPS (±10m parking bay accuracy)
    
    App-->>Customer: "Take 4 photos: front, rear, interior, charger"
    
    loop 4 photos
        Customer->>App: Capture photo
        App->>Gateway: POST /api/photos (multipart/form-data)
        Gateway->>PhotoService: UploadPhoto(rentalId, photoType, image)
        PhotoService->>GCS: Store photo with signed URL
        GCS-->>PhotoService: Photo URL (15-min expiry)
        PhotoService-->>Gateway: Photo ID
        Gateway-->>App: "Photo {i}/4 uploaded"
    end
    
    App->>Gateway: POST /api/rentals/{rentalId}/complete
    Gateway->>Rental: CompleteRental(rentalId, photoIds[], location, timestamp)
    
    Note over Rental: Calculate billing
    Rental->>DB: SELECT rental_session (startTime, endTime, baseRate)
    Rental->>Rental: Calculate: duration=127min, amount=€31.75
    
    Note over Rental,PhotoService: Parallel AI analysis (cleanliness + damage)
    par Cleanliness Check
        Rental->>PhotoService: AnalyzeCleanliness(interiorPhotoId)
        PhotoService->>GCS: Fetch photo via signed URL
        PhotoService->>VertexAI: POST /predict (AutoML Vision model)
        VertexAI-->>PhotoService: {cleanliness: "dirty", confidence: 0.78, issues: ["stains on seat"]}
        
        alt Confidence < 90% (Medium confidence)
            PhotoService->>Crew: CreateReviewTask<br/>(type: CleanlinessVerification, priority: P2)
            Crew-->>PhotoService: Task queued (30min SLA)
            PhotoService-->>Rental: {status: "pending_review", confidence: 0.78}
        else Confidence ≥ 90% (High confidence)
            PhotoService-->>Rental: {status: "clean", confidence: 0.92}
        end
    and Damage Detection
        Rental->>PhotoService: AnalyzeDamage(exteriorPhotoIds[])
        PhotoService->>GCS: Fetch photos
        PhotoService->>VertexAI: POST /predict (custom YOLO model)
        VertexAI-->>PhotoService: {damage: [{type: "dent", confidence: 0.87, bbox: {x:120,y:80...}, severity: "moderate"}]}
        
        alt Confidence > 85% (Auto-flag)
            PhotoService->>PhotoService: Estimate repair cost = €200
            PhotoService->>EventBus: Publish DamageDetected
            PhotoService-->>Rental: {status: "damage_confirmed", cost: €200}
        else Confidence 70-85% (Human review)
            PhotoService->>Crew: CreateReviewTask<br/>(type: DamageVerification, priority: P1)
            PhotoService-->>Rental: {status: "pending_review", confidence: 0.82}
        end
    end
    
    Note over Rental,Payment: Capture payment (pre-auth → charge)
    Rental->>Payment: CapturePayment<br/>(rentalAmount: €31.75, fines: €0, damage: €200)
    Payment-->>Rental: Payment successful
    
    Rental->>DB: BEGIN TRANSACTION
    Rental->>DB: UPDATE rental_session SET<br/>status='completed', endTime, finalAmount=€231.75
    Rental->>DB: INSERT payment_record<br/>(rental_id, base=€31.75, damage=€200)
    Rental->>DB: COMMIT
    
    Rental->>EventBus: Publish RentalCompleted
    Rental-->>Gateway: {finalAmount: €231.75, breakdown: {...}}
    Gateway-->>App: Receipt with itemized charges
    
    App-->>Customer: "Rental complete. €231.75 charged.<br/>⚠️ Damage fee €200 (dent detected)"
    
    opt Customer disputes damage fee
        Customer->>App: "Contest charge"
        App->>Gateway: POST /api/disputes<br/>(paymentId, reason, counterPhotos[])
        Note over Gateway: See SD-006 Dispute Resolution
    end
```

**Key Decisions:**
- Parallel AI analysis (cleanliness + damage) for performance
- Confidence thresholds trigger human review (ADR-0016)
- Photos stored 90 days (ADR-0017: GDPR compliance)
- Payment capture only after AI analysis completes
- Dispute window opens immediately after charge

---

## SD-004: AI Demand Forecasting & Crew Repositioning

**Functional Requirements:** FR#2H (Demand Forecasting), FR-CV-027 (Crew Tasks)  
**NFRs:** NFR_9 (AI: Forecasting 75-80% accuracy), NFR_3 (Performance: Forecast ≤60s)  
**ADRs:** ADR-0022 (Demand Forecasting Model), ADR-0011 (Model Evaluation), ADR-0006 (BigQuery)

```mermaid
sequenceDiagram
    participant Rental as Rental Lifecycle Service
    participant EventBus as Pub/Sub
    participant Forecasting as AI Demand Forecasting
    participant BigQuery as BigQuery (Historical Data)
    participant VertexAI as Vertex AI (Prophet Model)
    participant CrewDispatch as Crew Dispatch Service
    participant CrewApp as Crew Mobile App
    participant DB as PostgreSQL

    Note over Rental,DB: Event-driven forecasting triggered by rental lifecycle events
    
    loop Rental lifecycle events
        Rental->>EventBus: Publish RentalStarted / RentalCompleted
    end
    
    Note over EventBus,Forecasting: Batch processing every hour
    EventBus-->>Forecasting: Aggregated events (last 1h)
    
    Forecasting->>BigQuery: Query historical demand<br/>(last 90 days, by zone & vehicle type)
    BigQuery-->>Forecasting: Time-series data (rentals/hour)
    
    Forecasting->>BigQuery: Fetch exogenous features<br/>(weather, events, holidays)
    BigQuery-->>Forecasting: Weather forecast, event calendar
    
    Note over Forecasting,VertexAI: NFR_9: Prophet model per vehicle type
    Forecasting->>VertexAI: POST /predict (Prophet model: cars)
    VertexAI-->>Forecasting: 24h forecast [{hour, demand, confidence_interval}]
    
    Forecasting->>VertexAI: POST /predict (Prophet model: scooters)
    VertexAI-->>Forecasting: 24h forecast (weather-adjusted)
    
    Note over Forecasting: Identify repositioning opportunities
    Forecasting->>Forecasting: Compare forecast vs current distribution
    Forecasting->>Forecasting: Generate repositioning suggestions<br/>(move 5 cars from zone A to B before 8am)
    
    Forecasting->>EventBus: Publish DemandForecastUpdated
    Forecasting->>EventBus: Publish RepositioningRecommended<br/>(priority: high, deadline: 08:00)
    
    EventBus-->>CrewDispatch: RepositioningRecommended event
    
    CrewDispatch->>DB: BEGIN TRANSACTION
    CrewDispatch->>DB: INSERT crew_task<br/>(type: relocation, vehicleIds, source, target, priority, deadline)
    CrewDispatch->>DB: COMMIT
    
    CrewDispatch->>EventBus: Publish CrewTaskCreated
    
    Note over CrewApp: Crew member opens app
    CrewApp->>CrewDispatch: GET /api/tasks?assignedTo=me&status=pending
    CrewDispatch-->>CrewApp: Task list (sorted by priority, proximity)
    
    CrewApp-->>CrewApp: Show: "Move 5 cars from Suburban North<br/>to Financial District by 8am (predicted surge)"
    
    loop For each vehicle
        CrewApp->>CrewDispatch: POST /api/tasks/{taskId}/complete<br/>(vehicleId, completionPhoto, newLocation)
        CrewDispatch->>DB: UPDATE crew_task SET status='completed'
        CrewDispatch->>EventBus: Publish CrewTaskCompleted
    end
    
    Note over Forecasting: Post-analysis feedback loop
    Forecasting->>BigQuery: Compare forecast vs actual demand (hourly)
    
    alt MAPE > 30% for 4 weeks
        Forecasting->>Forecasting: Trigger model retraining alert
        Forecasting->>EventBus: Publish ModelDriftDetected
    end
```

**Key Decisions:**
- Hourly batch processing (not real-time) to balance accuracy vs cost
- Separate Prophet models per vehicle type (cars vs scooters)
- Weather impact weighted differently (scooters -60%, cars +10%)
- Crew tasks include deadline and priority for SLA enforcement
- Feedback loop detects model drift (ADR-0011)

---

## SD-005: Corporate Account Booking Flow

**Functional Requirements:** FR#3-1 (Corporate Accounts), FR#1-3 (Booking)  
**NFRs:** NFR_4 (Security: RBAC), NFR_7 (Compliance: 7-year financial records)  
**ADRs:** ADR-0001 (GCP IAM)

```mermaid
sequenceDiagram
    actor Employee as Corporate Employee
    participant App as Mobile App
    participant Gateway as API Gateway
    participant CorporateService as Corporate Account Service
    participant Booking as Booking Service
    participant Payment as Payment Gateway
    participant DB as PostgreSQL

    Note over Employee,DB: Corporate employee booking with account validation
    
    Employee->>App: Login (SSO: corporate email)
    App->>Gateway: POST /api/auth (OAuth 2.0 token)
    Gateway->>CorporateService: ValidateEmployeeAccess(token)
    CorporateService->>DB: SELECT corporate_account WHERE employee_id=...
    DB-->>CorporateService: {accountId, usageCap: €5000, currentUsage: €3200}
    CorporateService-->>Gateway: Employee authorized (Acme Corp)
    Gateway-->>App: JWT with corporateAccountId claim
    
    Employee->>App: Book vehicle (€50 estimated)
    App->>Gateway: POST /api/reservations<br/>{corporateAccountId, employeeId, ...}
    
    Gateway->>CorporateService: CheckUsageCap(accountId, estimatedAmount)
    CorporateService->>CorporateService: Calculate: €3200 + €50 = €3250 < €5000 ✓
    CorporateService-->>Gateway: Approved
    
    Gateway->>Booking: CreateReservation<br/>(corporateAccountId, employeeId, vehicleId)
    
    Note over Booking,Payment: No pre-auth (corporate billing)
    Booking->>DB: INSERT reservation (billTo: corporateAccount)
    Booking-->>Gateway: Reservation created
    Gateway-->>App: Booking confirmed
    
    App-->>Employee: "Booked. Billed to Acme Corp."
    
    Note over CorporateService: Monthly billing cycle
    loop Last day of month
        CorporateService->>DB: SELECT rentals WHERE<br/>corporateAccountId=... AND month=current
        DB-->>CorporateService: 127 rentals, €8,340 total
        
        CorporateService->>CorporateService: Generate consolidated invoice<br/>(breakdown by employee, vehicle type)
        
        CorporateService->>Payment: ChargeConsolidatedInvoice<br/>(accountId, amount, invoice PDF)
        Payment-->>CorporateService: Payment successful
        
        CorporateService->>EventBus: Publish CorporateUsageReportGenerated
    end
```

**Key Decisions:**
- No pre-authorization for corporate accounts (credit line model)
- Usage cap enforced at booking time (prevent overages)
- Consolidated monthly billing (not per-rental charges)
- SSO integration with corporate IdP (OAuth 2.0)
- 7-year invoice retention (NFR_7 compliance)

---

## SD-006: Dispute Resolution Workflow

**Functional Requirements:** FR#1-8 (Fees), FR#1-9 (Loyalty), Domain: Dispute aggregate  
**NFRs:** NFR_7 (Compliance: Audit trail), NFR_2 (Reliability: 5-day SLA)  
**ADRs:** ADR-0017 (Photo Retention), ADR-0001 (GCP IAM for operations access)

```mermaid
sequenceDiagram
    actor Customer
    participant App as Mobile App
    participant Gateway as API Gateway
    participant Dispute as Dispute Service
    participant GCS as Cloud Storage
    participant Operations as Operations Dashboard
    participant Payment as Payment Service
    participant EventBus as Pub/Sub
    participant DB as PostgreSQL

    Note over Customer,DB: 48-hour dispute submission window
    
    Customer->>App: "Contest €200 damage charge"
    App-->>Customer: "Upload evidence (photos, explanation)"
    
    Customer->>App: Provide counter-photos + explanation
    App->>Gateway: POST /api/disputes<br/>{paymentId, reason, evidencePhotos[], description}
    
    Gateway->>Dispute: CreateDispute(request)
    
    Dispute->>GCS: Store evidence photos (90-day retention)
    GCS-->>Dispute: Photo URLs
    
    Dispute->>DB: BEGIN TRANSACTION
    Dispute->>DB: INSERT dispute<br/>(paymentId, customerId, reason, evidenceUrls, submittedAt)
    Dispute->>DB: UPDATE payment_record SET status='disputed'
    Dispute->>DB: COMMIT
    
    Dispute->>EventBus: Publish DisputeRaised
    
    Dispute-->>Gateway: {disputeId, status: 'under_review', deadline: 'Nov 1'}
    Gateway-->>App: "Dispute submitted. Decision within 5 days."
    
    Note over Operations: Operations team reviews (human-in-the-loop)
    EventBus-->>Operations: DisputeRaised notification
    
    Operations->>Dispute: GET /api/disputes/{disputeId}
    Dispute->>GCS: Fetch original AI photos + customer evidence
    Dispute-->>Operations: Full evidence package<br/>(AI annotated photos, confidence scores, customer counter-photos)
    
    alt Dispute Approved
        Operations->>Dispute: PUT /api/disputes/{disputeId}/decision<br/>{approved: true, refundAmount: €200, reason: "..."}
        
        Dispute->>Payment: IssueRefund(paymentId, amount: €200)
        Payment-->>Dispute: Refund processed
        
        Dispute->>DB: UPDATE dispute SET<br/>status='approved', reviewedBy, reviewedAt, decision
        
        Dispute->>EventBus: Publish DisputeApproved
        
        EventBus-->>App: Push notification
        App-->>Customer: "Dispute approved. €200 refunded."
    else Dispute Rejected
        Operations->>Dispute: PUT /api/disputes/{disputeId}/decision<br/>{approved: false, reason: "AI analysis confirmed damage"}
        
        Dispute->>DB: UPDATE dispute SET<br/>status='rejected', reviewedBy, reviewedAt, decision
        
        Dispute->>EventBus: Publish DisputeRejected
        
        App-->>Customer: "Dispute rejected. Charge stands.<br/>You may escalate within 7 days."
        
        opt Customer escalates (one-time allowed)
            Customer->>App: "Escalate to manager"
            App->>Dispute: POST /api/disputes/{disputeId}/escalate
            Dispute->>DB: UPDATE dispute SET escalated=true
            Dispute->>EventBus: Publish DisputeEscalated
            Note over Operations: Senior operations review (extended SLA)
        end
    end
    
    Note over Dispute,DB: NFR_7: 7-year audit trail retention
    Dispute->>DB: Archive dispute record (immutable, compliance)
```

**Key Decisions:**
- 48-hour submission window (urgency to prevent fraud)
- 5-day decision SLA (balance thoroughness vs customer satisfaction)
- Evidence photos stored 90 days (ADR-0017)
- One escalation allowed (prevent abuse)
- Full audit trail (compliance requirement)

---

## SD-007: Recurring Reservation Management

**Functional Requirements:** FR#1-3 (Booking: Recurring reservations)  
**NFRs:** NFR_1 (Scalability: Handle 6-month patterns), NFR_3 (Performance: Skip within 24h)  
**Domain:** Recurring Pattern (value object in Reservation)

```mermaid
sequenceDiagram
    actor Customer
    participant App as Mobile App
    participant Gateway as API Gateway
    participant Booking as Booking Service
    participant Scheduler as Cloud Scheduler
    participant EventBus as Pub/Sub
    participant DB as PostgreSQL

    Note over Customer,DB: Create standing reservation (e.g., every Monday 8am-6pm)
    
    Customer->>App: "Create recurring booking"
    App-->>Customer: "Frequency, duration, end date?"
    Customer->>App: "Every Monday, 8am-6pm, for 6 months"
    
    App->>Gateway: POST /api/reservations/recurring<br/>{vehicleId, pattern: {frequency: "weekly", dayOfWeek: "Monday", ...}}
    
    Gateway->>Booking: CreateRecurringReservation(request)
    
    Booking->>Booking: Generate occurrence dates<br/>(next 26 Mondays)
    
    Booking->>DB: BEGIN TRANSACTION
    Booking->>DB: INSERT reservation<br/>(type: 'recurring_parent', patternRules, occurrenceCount: 26)
    
    loop For first 4 weeks (eager creation)
        Booking->>DB: INSERT reservation<br/>(type: 'recurring_occurrence', parentId, date)
    end
    
    Booking->>DB: COMMIT
    
    Booking->>Scheduler: Create cron job<br/>(every Sunday 6pm: generate next week's occurrence)
    
    Booking-->>Gateway: Recurring reservation created
    Gateway-->>App: "Standing booking created. 26 occurrences."
    
    Note over Scheduler: Weekly cron execution
    loop Every Sunday 6pm
        Scheduler->>Booking: GenerateNextOccurrence(recurringReservationId)
        Booking->>DB: INSERT reservation (occurrence for upcoming Monday)
        Booking->>EventBus: Publish RecurringOccurrenceCreated
    end
    
    Note over Customer: Customer needs to skip one week
    Customer->>App: "Skip next Monday (Nov 20)"
    App->>Gateway: POST /api/reservations/{occurrenceId}/skip
    
    Gateway->>Booking: SkipOccurrence(occurrenceId, reason)
    
    Booking->>Booking: Validate: ≥24h notice required
    
    Booking->>DB: BEGIN TRANSACTION
    Booking->>DB: UPDATE reservation SET status='skipped', skipReason, skippedAt
    Booking->>DB: UPDATE vehicle SET availability='free' (for that slot)
    Booking->>DB: COMMIT
    
    Booking->>EventBus: Publish RecurringOccurrenceSkipped
    
    Booking-->>Gateway: Skip confirmed
    Gateway-->>App: "Nov 20 skipped. Vehicle available for others."
    
    Note over Booking: Pattern expiration
    alt Reached 6-month end date
        Booking->>DB: UPDATE reservation SET<br/>status='expired', completedOccurrences: 24, skippedOccurrences: 2
        Booking->>EventBus: Publish RecurringPatternExpired
    end
```

**Key Decisions:**
- Eager creation (4 weeks ahead) + lazy generation (weekly cron)
- 24h skip notice enforced (prevent last-minute cancellations)
- Parent reservation tracks pattern rules
- Occurrences are individual reservations (allows modification)
- Auto-expiration at end date or max occurrences

---

## Summary: Sequence Coverage

| Sequence | Status | Critical Path | NFR Coverage | ADR References |
|----------|--------|---------------|--------------|----------------|
| SD-001: Booking Flow | ✅ Complete | Yes | NFR_3, NFR_4, NFR_1 | ADR-0001, ADR-0002 |
| SD-002: Unlock & Start | ✅ Complete | Yes | NFR_3, NFR_2 | ADR-0002, ADR-0007 |
| SD-003: Return & AI Photo | ✅ Complete | Yes | NFR_9, NFR_3 | ADR-0003, ADR-0014, ADR-0016, ADR-0017 |
| SD-004: Demand Forecasting | ✅ Complete | No (ops optimization) | NFR_9, NFR_3 | ADR-0022, ADR-0011, ADR-0006 |
| SD-005: Corporate Booking | ✅ Complete | Yes (B2B) | NFR_4, NFR_7 | ADR-0001 |
| SD-006: Dispute Resolution | ✅ Complete | No (exception path) | NFR_7, NFR_2 | ADR-0017, ADR-0001 |
| SD-007: Recurring Reservations | ✅ Complete | Yes (power user feature) | NFR_1, NFR_3 | ADR-0002 |

