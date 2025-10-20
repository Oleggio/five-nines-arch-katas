# Car/Van Rental - Functional Requirements

## Overview
This document details functional requirements for the Car/Van Rental bounded context, organized by business capability. Each requirement includes traceability to business goals, acceptance criteria, and architectural dependencies.

**Related Documents:**
- Business Context: `1_Business_Context.md`
- Non-Functional Requirements: `3_NFRs.md`
- Domain Model: `4_Domain_Model.md` (ubiquitous language, aggregates, domain events)
- Integration Requirements: `5_Integration_Requirements.md`

**Note:** 
- Sequence diagrams and component diagrams are maintained in the `/hld/` (High-Level Design) folder
- This document focuses on functional requirements with business context and acceptance criteria
- Detailed data models and JSON schemas are yet to be defined in future design phases

---

## 1. Booking & Reservation Management

### FR-CV-001: Advanced Booking with Charge Sufficiency Validation
**Business Goal:** Prevent customer dissatisfaction from insufficient charge scenarios

**Description:** Users can book cars/vans up to 7 days in advance. System MUST validate vehicle has sufficient charge for requested rental duration before allowing booking.

**Source:** Transcript Q&A - "Won't let people book a car/van unless it's got enough charge"

**Acceptance Criteria:**
- [ ] User searches availability for vehicle type, location, date/time, duration
- [ ] System calculates required range based on duration (assume average 50km/hour usage)
- [ ] System adds 20% buffer to required range for safety margin
- [ ] Booking rejected if vehicle's current range < required range
- [ ] User shown alternative vehicles with sufficient charge
- [ ] Booking creates reservation with payment pre-authorization hold
- [ ] System prevents double-booking with pessimistic locking

**Business Rules:**
- Maximum advance booking: 7 days
- Minimum rental duration: 1 hour
- Maximum single rental: 7 days (168 hours)
- Charge buffer: 20% above estimated consumption
- Pre-auth amount: (Duration × Rate) + 20% buffer + Maximum potential fines (€200)

**Error Scenarios:**
- Insufficient charge → Show alternative vehicles or suggest partner charging
- No vehicles available → Offer waitlist or alternative time slots
- Payment pre-auth fails → Cannot complete booking

**Dependencies:**
- Vehicle Status Manager (real-time range data)
- Payment Gateway (pre-authorization)
- Customer Management (identity, payment method)

**Related ADRs:** 
- ADR-TBD: Database selection for zero double-booking guarantee (ACID compliance required)
- ADR-TBD: Charge calculation algorithm (conservative vs optimistic)

---

### FR-CV-002: Recurring/Standing Reservations
**Business Goal:** Drive habitual usage and predictable revenue

**Description:** Customers can create recurring booking patterns (e.g., "every Monday 8am-6pm") to reserve vehicles automatically.

**Source:** Business Context - Habitual Usage Features

**Acceptance Criteria:**
- [ ] User creates recurring pattern with: frequency (daily/weekly), day(s), time, duration, vehicle preference
- [ ] System pre-books vehicles based on pattern up to 7 days in advance
- [ ] User receives confirmation 24 hours before each occurrence
- [ ] User can skip individual occurrences without penalty (24h notice)
- [ ] User can pause/resume/cancel recurring pattern
- [ ] System handles conflicts (pattern vehicle unavailable) by auto-assigning alternative or notifying user
- [ ] Pre-authorization renewed for each occurrence

**Business Rules:**
- Maximum pattern duration: 6 months
- Minimum skip notice: 24 hours (otherwise charged)
- Pattern priority: Recurring bookings get priority over ad-hoc bookings during high demand
- Auto-cancellation: Pattern cancelled after 3 consecutive skips

**Dependencies:**
- Booking Service (reservation creation)
- Notification Service (24h reminders)
- Demand Forecasting (utilization patterns)

---

### FR-CV-003: Quick Rebooking & Favorites
**Business Goal:** Reduce booking friction for repeat customers

**Description:** One-tap rebooking of previous trips and saved favorites for frequent usage patterns.

**Source:** Business Context - Habitual Usage Features

**Acceptance Criteria:**
- [ ] User's booking history stored with full context (vehicle, location, time, duration)
- [ ] "Rebook" button on completed rentals creates new reservation with same parameters
- [ ] User can save bookings as "Favorites" with custom labels
- [ ] Favorites support flexible rebooking (same time different day, same route different time)
- [ ] System suggests "Frequently Booked" based on historical patterns
- [ ] Smart defaults pre-fill booking form based on user's typical usage

**Business Rules:**
- History retention: 24 months
- Maximum favorites: 10 per customer
- Smart suggestions require minimum 3 similar trips

**UI/UX Requirements:**
- Favorites accessible from home screen
- Rebook flow max 2 taps (select favorite → confirm)
- Show availability status before user commits

**Dependencies:**
- Customer Profile Service (preferences storage)
- Booking Service (reservation creation)

---

### FR-CV-004: Booking Extension with Conflict Checking
**Business Goal:** Flexible rental duration while preventing double-booking

**Description:** Customers can request to extend active rentals. System checks for conflicting next bookings before approval.

**Source:** Transcript Q&A - "For cars and vans, yes, they can ask to extend... but somebody else might have booked that vehicle"

**Acceptance Criteria:**
- [ ] User requests extension from mobile app during active rental
- [ ] System checks if vehicle has next reservation within requested extension period
- [ ] If no conflict: Auto-approve extension, update billing, send confirmation
- [ ] If conflict exists: Deny extension, show conflict time, suggest alternative vehicles nearby
- [ ] Extension updates pre-authorization hold for additional amount
- [ ] System logs all extension requests for demand analysis

**Business Rules:**
- Maximum extension: Original duration × 2 (e.g., 4h booking can extend max 8h total)
- Extension pricing: Same per-minute rate as original booking
- Minimum extension increment: 30 minutes
- Extension deadline: Cannot extend beyond 7-day maximum rental

**Conflict Resolution:**
- Next booking exists: System calculates required buffer time = Return time (30 min) + Charging time (variable based on current charge level)
- Charging time estimation: Based on vehicle's current charge, target charge for next booking, and charger capacity
- Extension allowed only if: Current rental end + Extension duration + Buffer time ≤ Next booking start
- Example: Next booking needs 80% charge, vehicle at 40%, charging takes ~2 hours → Buffer = 30min + 120min = 150min
- If insufficient time: Deny extension, suggest shorter duration or alternative vehicle

**Error Scenarios:**
- Conflict exists → Suggest nearby alternative vehicles or shorter extension
- Payment authorization fails → Extension denied, user must return at original time
- Vehicle malfunction during rental → Extension not allowed, roadside assistance dispatched

**Dependencies:**
- Rental Lifecycle Service (active rental tracking)
- Booking Service (reservation conflict checking)
- Vehicle Status Manager (current charge level, charging time estimation)
- Payment Service (pre-auth adjustment)
- Notification Service (conflict alerts)

---

### FR-CV-018: Reservation Cancellation & Refunds
**Business Goal:** Customer flexibility while protecting revenue

**Description:** Customers can cancel reservations anytime without penalty. System releases pre-authorization and makes vehicle available.

**Source:** Transcript Q&A - "Customers can cancel basically anytime without penalty"

**Acceptance Criteria:**
- [ ] Customer can cancel reservation via mobile app or customer support
- [ ] Cancellation allowed until rental session starts (vehicle unlocked)
- [ ] System releases pre-authorization hold immediately upon cancellation
- [ ] Vehicle availability status updated to "Available" for rebooking
- [ ] Customer receives cancellation confirmation via email/SMS
- [ ] Cancellation logged for analytics (cancellation patterns, reasons)
- [ ] Recurring reservations: Individual occurrences can be skipped without canceling entire pattern

**Business Rules:**
- No cancellation fee (as stated in transcript)
- Cancellation not allowed after rental starts (must use return process instead)
- Recurring pattern: 24-hour notice required to skip individual occurrence (otherwise charged)
- Pre-auth release: Within 5 minutes of cancellation (real-time processing)
- Ghost bookings: Multiple cancellations (>5 in 30 days) may trigger account review

**Cancellation Reasons (Optional Collection):**
- Plans changed
- Found alternative transportation
- Vehicle not suitable
- Pricing concern
- Other (free text)

**Impact on Demand Forecasting:**
- Cancellation events published to AI forecasting system
- High cancellation rates for specific times/locations indicate demand mismatch
- Helps optimize vehicle positioning and pricing strategies

**Error Scenarios:**
- Rental already started → Cannot cancel, must return vehicle normally
- Pre-auth release fails → Retry up to 3 times, escalate to payment team
- Recurring pattern cancellation → Confirm if canceling entire pattern or single occurrence

**Dependencies:**
- Booking Service (reservation status management)
- Payment Service (pre-auth release)
- Vehicle Status Manager (availability update)
- Notification Service (confirmation messages)
- Demand Forecasting (cancellation event publishing)

**Related ADRs:**
- ADR-TBD: Cancellation window policies (immediate vs grace period)
- ADR-TBD: Ghost booking prevention strategies

---

### FR-CV-024: Booking Modification (Pre-Rental)
**Business Goal:** Customer flexibility, reduced cancellations, improved retention

**Description:** Customers can modify reservation details (date, time, vehicle type, pickup/return location) before rental starts without canceling and rebooking.

**Source:** Operational best practice - Common customer need to adjust plans

**Acceptance Criteria:**
- [ ] Customer can modify reservation via mobile app or customer support
- [ ] Modifiable fields: Start date/time, end date/time, vehicle type, pickup location, return location
- [ ] Modification allowed until 1 hour before scheduled start time
- [ ] System validates new booking parameters (availability, pricing, conflicts)
- [ ] Price adjustment calculated automatically (difference charged/refunded)
- [ ] Original pre-authorization adjusted if price increases
- [ ] Modification confirmation sent via email/SMS with updated details
- [ ] Booking reference number remains unchanged
- [ ] Modification history tracked for audit

**Modification Scenarios:**

**1. Time Extension/Reduction:**
- Extend rental duration → Calculate price difference, adjust pre-auth, confirm availability
- Shorten rental duration → Calculate refund amount, release excess pre-auth, update vehicle schedule
- Validation: New end time must not conflict with subsequent bookings

**2. Date/Time Change:**
- Move reservation to different date/time → Check vehicle availability, recalculate pricing (demand-based)
- Price may increase or decrease based on new time slot demand
- Original vehicle prioritized, alternative suggested if unavailable

**3. Vehicle Type Change:**
- Upgrade (car → van) → Calculate price difference, check availability, adjust pre-auth
- Downgrade (van → car) → Refund difference, release excess pre-auth
- Vehicle-specific features preserved (e.g., child seat, bike rack)

**4. Location Change:**
- Change pickup location → Validate new location has availability
- Change return location → Recalculate any location-based fees
- Cross-border implications checked if applicable

**Business Rules:**
- Modification deadline: 1 hour before scheduled start (buffer for crew preparation)
- Price lock: Original booking price protected if modified >24 hours in advance
- Price adjustment: Dynamic pricing applies if modified <24 hours before start
- Multiple modifications: Allowed (no limit), but each modification extends 1-hour deadline
- Recurring bookings: Can modify template (affects future occurrences) or individual occurrence
- Availability: If modified parameters unavailable, system suggests alternatives or modification fails

**Pricing Impact:**
- Price increase: Charge difference to original payment method, adjust pre-auth
- Price decrease: Refund difference or adjust pre-auth down (customer choice)
- Modification fee: None (free modifications to encourage flexibility over cancellation)
- Pricing transparency: "Original price: €45, New price: €52, Difference: +€7"

**Validation Rules:**
- Start time: Cannot be in the past, must be ≥1 hour from now
- End time: Must be after start time, maximum rental duration applies (7 days)
- Vehicle availability: Real-time check, 5-minute reservation lock during modification
- Payment method: Must be valid and have sufficient funds for price increases
- Customer eligibility: Account in good standing, no outstanding disputes

**Error Scenarios:**
- Vehicle unavailable for new date/time → Suggest similar vehicles or alternative times
- Payment authorization fails → Modification denied, original booking retained
- Modification within 1-hour window → "Too late to modify, contact support for assistance"
- Concurrent modification (race condition) → "Booking being modified by another session, try again"

**Customer Communication:**
- Modification confirmation: Email/SMS with all updated details
- Price change notification: Clear breakdown of charges/refunds
- Alternative suggestions: If preferred modification unavailable
- Reminder: "You can still cancel free of charge if needed"

**Analytics:**
- Modification patterns: Which fields most commonly changed
- Timing: How far in advance modifications occur
- Conversion impact: Modifications preventing cancellations
- Revenue impact: Price adjustments (net gain/loss)
- Popular upgrade paths (car → van frequency)

**Dependencies:**
- Booking Service (reservation modification logic)
- Vehicle Status Manager (availability validation)
- Payment Service (pre-auth adjustment, price difference processing)
- Pricing Engine (recalculation for new parameters)
- Notification Service (modification confirmations)
- Audit Service (modification history tracking)

**Related ADRs:**
- ADR-TBD: Modification deadline (1 hour vs flexible)
- ADR-TBD: Price lock policy (protect original price vs dynamic repricing)
- ADR-TBD: Concurrent modification handling (optimistic vs pessimistic locking)

---

## 2. Vehicle Access & Lifecycle

### FR-CV-005: Dual Access Control (Phone NFC + Remote Unlock)
**Business Goal:** Reliable access with fallback mechanism

**Description:** Customers use their smartphone's NFC capability via mobile app as primary unlock method, with remote unlock API as fallback.

**Source:** Transcript - "Customers have a mobile device that is NFC capable... put their mobile device next to the vehicle to unlock it"

**Acceptance Criteria:**
- [ ] Mobile app leverages phone's built-in NFC hardware
- [ ] Customer taps phone (with app open or in background) on vehicle's NFC reader → Vehicle unlocks within 2 seconds
- [ ] NFC unlock transmits: Customer ID, reservation ID, timestamp (signed with app credentials)
- [ ] If NFC fails: Mobile app "Remote Unlock" button triggers API call to Vehicle IoT Gateway
- [ ] Unlock succeeds only if: Reservation active, customer authorized, vehicle available
- [ ] Unlock event triggers rental session start (billing begins)
- [ ] Unlock logs: timestamp, method (NFC/Remote API), location, vehicle ID

**Business Rules:**
- Unlock window: 15 minutes before reservation start time to 30 minutes after
- After 30 minutes late: Reservation auto-cancelled, pre-auth released (minus no-show fee)
- Remote unlock rate limit: Max 5 attempts per minute (prevent abuse)

**Technical Requirements:**
- NFC protocol: ISO 14443 Type A/B (industry standard for contactless)
- NFC payload: Encrypted token with reservation validation data
- API timeout: 5 seconds max for remote unlock
- Fallback: If both NFC + API fail → Customer support notification, manual unlock by operations

**Error Handling:**
- Phone NFC disabled/unavailable → Guide customer to enable NFC or use remote unlock
- Vehicle offline → Queue unlock command, retry when vehicle reconnects
- Reservation expired → Deny unlock, offer rebooking
- Vehicle in maintenance mode → Deny unlock, show error, suggest alternative

**Dependencies:**
- Vehicle IoT Gateway (unlock commands)
- Rental Lifecycle Service (session initiation)
- Customer Management (authentication, reservation validation)

**Related ADRs:**
- ADR-TBD: Synchronous unlock API vs async messaging (MQTT)
- ADR-TBD: NFC security (token encryption, replay attack prevention)

---

### FR-CV-022: Late/No-Show Fee Processing
**Business Goal:** Revenue protection, discourage no-shows, optimize vehicle utilization

**Description:** System automatically detects late arrivals and no-shows, applies fees, and makes vehicle available for rebooking.

**Source:** Mentioned in FR-CV-005 - "After 30 minutes late: Reservation auto-cancelled, pre-auth released (minus no-show fee)"

**Acceptance Criteria:**
- [ ] Grace period: 15 minutes after reservation start time (no penalty)
- [ ] Late arrival (15-30 min): Customer can still unlock vehicle, no fee
- [ ] No-show (>30 min): Reservation auto-cancelled, no-show fee charged
- [ ] Customer notified at 15 minutes (reminder to unlock or cancel)
- [ ] No-show fee: €25 (flat fee, configurable by region)
- [ ] Fee charged against pre-authorization, remainder released
- [ ] Vehicle status updated to "Available" for immediate rebooking
- [ ] Customer can dispute no-show fee if legitimate reason (e.g., traffic, emergency)

**Late Arrival Scenarios:**
- **0-15 minutes late:** No penalty, rental starts when unlocked
- **15-30 minutes late:** Warning SMS sent, rental still available
- **30+ minutes late:** Auto-cancelled, no-show fee applied

**No-Show Fee Justification:**
- Compensates for lost revenue opportunity
- Discourages habitual no-shows
- Lower than full rental cost (fair to customer)
- Waived for first offense per customer (goodwill)

**Business Rules:**
- Grace period: 15 minutes (no fee)
- No-show threshold: 30 minutes after reservation start
- Fee amount: €25 (or 1 hour equivalent rental cost, whichever lower)
- First-time waiver: First no-show in 12 months automatically waived
- Recurring no-shows: >3 in 6 months may trigger account restrictions
- Dispute window: 48 hours (links to FR-CV-020 Dispute Resolution)

**Customer Communication:**
- Booking confirmation: "Please arrive within 15 minutes of start time"
- 15-minute reminder: "Reservation starts in 15 minutes. Unlock vehicle or cancel to avoid fees."
- 30-minute no-show: "Reservation cancelled. No-show fee of €25 applied. Contact support to dispute."

**Exception Handling:**
- Traffic/Accident: Customer can report via app, extends grace period
- Vehicle malfunction: No-show fee waived if vehicle unavailable
- App/NFC failure: Customer contacts support, manual unlock, no fee
- Roadside assistance called: No-show fee waived

**Analytics & Patterns:**
- Track no-show rate by customer, location, time of day
- High no-show locations may indicate access issues
- Chronic no-show customers flagged for review
- No-show reduction impact on utilization rates

**Integration with Recurring Reservations:**
- Recurring pattern: 24-hour skip notice required (no fee)
- Less than 24 hours: No-show fee applies if not unlocked
- Recurring no-shows (>2) trigger pattern cancellation

**Error Scenarios:**
- Customer unlocks at 29 minutes → Rental starts, no fee
- Customer unlocks at 31 minutes → Rental blocked, no-show fee already applied
- Fee charge fails → Retry processing, escalate if unsuccessful
- Customer disputes → Routed to FR-CV-020 Dispute Resolution workflow

**Dependencies:**
- Booking Service (reservation status, auto-cancellation)
- Rental Lifecycle Service (unlock tracking)
- Payment Service (no-show fee charging)
- Notification Service (reminders, cancellation notices)
- Vehicle Status Manager (availability update)

**Related ADRs:**
- ADR-TBD: No-show fee pricing strategy (flat vs proportional)
- ADR-TBD: Grace period optimization (customer satisfaction vs utilization)

---

**Note:** FR-CV-025 (Vehicle Condition Inspection - Pre-Rental) has been moved to **Section 7: AI-Powered Features** as **FR-AI-003**.

---

### FR-CV-006: Cross-Border Usage with Origin City Return
**Business Goal:** Enable EU mobility while preventing fleet imbalance

**Description:** Vehicles can be driven cross-border within EU but MUST be returned to designated parking bays in origin city.

**Source:** Transcript Q&A - "People can take a car from Berlin and drive it to Amsterdam... but they have to bring it back to a designated return location in the city where they got it"

**Acceptance Criteria:**
- [ ] System tracks vehicle's origin city at rental start
- [ ] GPS telemetry monitors cross-border travel (country boundary detection)
- [ ] Return process validates: Parking bay in same city as pickup
- [ ] If returned to wrong city: Return rejected, customer notified, fine applied
- [ ] Cross-border trips flagged for demand forecasting analysis

**Business Rules:**
- Origin city defined by pickup location's administrative boundary
- Acceptable return locations: Any designated bay within origin city limits
- Cross-border fine: Not applicable (allowed)
- Wrong city return fine: €150 + cost of vehicle repatriation

**Geo-Validation:**
- City boundaries defined in geospatial database (GeoJSON polygons)
- Return validation checks: Parking bay coordinates within origin city polygon
- Buffer zone: 5km tolerance for city boundary edge cases

**Error Scenarios:**
- Return in wrong city → Reject return, show map of correct return locations
- Customer disputes city boundary → Support escalation with GPS evidence
- Vehicle breakdown in foreign country → Roadside assistance coordinates return

**Dependencies:**
- Mapping Service (city boundary data, Google Maps/HERE)
- Telemetry Service (cross-border detection)
- Fleet Management (vehicle repatriation)

---

## 3. Return Verification & Condition Assessment

### FR-CV-007: Return Verification (Parking + Charger + Photos)
**Business Goal:** Ensure vehicles ready for next customer, enforce return policies

**Description:** Multi-step return verification requiring correct parking bay, EV charger connection, and photographic proof.

**Source:** Transcript - "They have to be plugged into the EV chargers... we require customers to take a photo as proof of return"

**Acceptance Criteria:**
- [ ] Customer initiates return from mobile app
- [ ] System validates GPS location matches designated parking bay (±10m accuracy)
- [ ] Customer submits 4 photos: Front exterior, rear exterior, interior, charger connection
- [ ] AI analyzes charger photo for proper connection (plug inserted, cable not damaged)
- [ ] AI analyzes parking photo for correct bay position (within marked lines)
- [ ] All validations pass → Return accepted, billing calculated, vehicle status → "Available"
- [ ] Any validation fails → Return rejected, customer notified with corrective actions

**Photo Requirements:**
- Minimum resolution: 1920×1080
- Maximum file size: 5MB per photo
- Format: JPEG/PNG
- Metadata: GPS coordinates, timestamp (verified against server time)

**AI Validation Criteria:**
- Charger connection: Plug visible, properly inserted, no damage
- Parking position: Vehicle within bay markings, not blocking adjacent spaces
- Cleanliness: Assessed separately (see FR-AI-001: Vehicle Cleanliness Verification)

**Business Rules:**
- Wrong location fine: €50 (if outside designated bay but within city)
- Charger not connected fine: €25
- Late return fine: €10 per 15-minute block after grace period
- Grace period: 15 minutes after reservation end time

**Validation Fallback:**
- If AI confidence < 80%: Flag for human review (operations team)
- Review SLA: Within 30 minutes during business hours, 2 hours off-hours
- Customer dispute window: 48 hours with counter-evidence photos

**Error Scenarios:**
- GPS inaccurate → Manual location verification by operations
- Photo upload fails → Allow retry, extend grace period
- AI system offline → Fallback to manual review for all returns

**Dependencies:**
- Photo Storage Service (cloud storage)
- AI Photo Analysis Service (Vertex AI Vision API)
- Telemetry Service (GPS validation)
- Payment Service (fine calculation)

**Related ADRs:**
- ADR-TBD: AI photo verification vs manual inspection (cost/accuracy trade-off)
- ADR-TBD: Photo storage strategy (retention, GDPR compliance)

---

**Note:** FR-CV-008 (Vehicle Cleanliness Verification) has been moved to **Section 7: AI-Powered Features** as **FR-AI-001**.

---

## 4. Payment & Pricing

### FR-CV-009: Per-Minute Billing with Pre-Authorization
**Business Goal:** Transparent pricing, prevent payment fraud

**Description:** Time-based billing with upfront pre-authorization hold, final charge calculated at return.

**Source:** Transcript Q&A - "We do pre-authorization... then at the end charge them for actual usage"

**Acceptance Criteria:**
- [ ] At booking: Calculate estimated cost (duration × rate) + 20% buffer
- [ ] Place pre-authorization hold on customer's payment method
- [ ] Pre-auth must succeed for booking to complete
- [ ] At rental start: Billing begins (per-minute clock starts)
- [ ] During rental: Real-time cost accumulation visible in app
- [ ] At return: Calculate final charge (minutes used × rate + any fines)
- [ ] Charge final amount, release unused pre-auth within 24 hours
- [ ] Customer receives itemized receipt via email

**Billing Calculation:**
```
Base Charge = (Rental Duration in Minutes) × (Per-Minute Rate for Vehicle Type)
Fines = Late Fee + Wrong Location Fee + Charger Fee + Cleaning Fee
Total Charge = Base Charge + Fines
```

**Pre-Authorization Logic:**
```
Max Possible Cost = (Booked Duration × 1.5) × Rate  // 50% extension buffer
Max Fines = €200  // Late + Location + Charger + Cleaning
Pre-Auth Amount = Max Possible Cost + Max Fines
```

**Business Rules:**
- Billing precision: Per minute (not rounded)
- Rate variation: By vehicle type, loyalty tier, time of day (future)
- Pre-auth expiry: 7 days (for maximum rental duration)
- Failed charge: 3 retry attempts over 48 hours, then collections process

**Error Scenarios:**
- Pre-auth fails at booking → Booking cancelled, customer notified
- Pre-auth insufficient for extension → Extension denied
- Final charge fails → Vehicle marked unavailable until payment resolved

**Dependencies:**
- Payment Gateway (Stripe/Adyen - see ADR)
- Rental Lifecycle Service (duration tracking)
- Customer Management (payment method)

**Related ADRs:**
- ADR-TBD: Payment gateway vendor selection (Stripe vs alternatives)
- ADR-TBD: Pre-auth vs pay-later models

---

### FR-CV-028: Fine Calculation Engine (Consolidated)
**Business Goal:** Automated fee assessment, consistent policy enforcement, revenue protection

**Description:** Centralized fine calculation engine that applies business rules for all fine types (late return, wrong location, charger blocking, cleaning, no-show) with transparent itemization.

**Source:** Consolidated from multiple requirements - Operational requirement for consistent fine management

**Acceptance Criteria:**
- [ ] Single service calculates all fine types with consistent rules
- [ ] Fine calculation triggered by: Late return, wrong location detection, charger blocking, AI cleanliness assessment, no-show timeout
- [ ] Each fine includes: Type, amount, reason, timestamp, evidence (photos/GPS), dispute link
- [ ] Customer receives itemized breakdown of all fines with rental receipt
- [ ] First-time offender waivers applied automatically per policy
- [ ] Fines capped at maximum threshold (€200 total per rental)
- [ ] Dispute workflow integration: Customers can contest fines with evidence
- [ ] Analytics dashboard for operations: Fine frequency, revenue, patterns

**Fine Types & Rules:**

**1. Late Return Fee:**
- **Rule:** Vehicle not returned within grace period (15 minutes after booked end time)
- **Calculation:** €0.50/minute after grace period
- **Cap:** €100 or until next booking start (whichever lower)
- **Source:** FR-CV-003 (Rental extension & conflict resolution)
- **Waiver:** First late return <30 minutes waived (warning only)

**2. Wrong Location Fee:**
- **Rule:** Vehicle returned outside origin city boundaries
- **Calculation:** €50 flat fee + crew retrieval cost (€1.50/km)
- **Cap:** €300 total
- **Source:** FR-CV-006 (Cross-border usage with origin city return)
- **Waiver:** Emergency situations (medical, accident) reviewed by support

**3. Charger Blocking Fee:**
- **Rule:** Vehicle left at charger >60 minutes after reaching 80% charge during rental
- **Calculation:** €10 flat fee after 1-hour grace period
- **Cap:** €10 per incident
- **Source:** FR-CV-011 (Charger-blocking detection & fine)
- **Waiver:** First offense waived with educational notification

**4. Cleaning Fee:**
- **Rule:** AI-detected excessive mess at return inspection
- **Calculation:** Minor (€50), Major (€100), Severe (€150)
- **Cap:** €150
- **Source:** FR-AI-001 (AI-based cleanliness inspection)
- **Waiver:** Gold tier first offense waived

**5. No-Show Fee:**
- **Rule:** Customer doesn't unlock vehicle within 30 minutes of reservation start
- **Calculation:** €25 flat fee
- **Cap:** €25
- **Source:** FR-CV-022 (Late/No-Show Fee Processing)
- **Waiver:** First no-show waived (all tiers)

**6. Damage Fee:**
- **Rule:** New damage detected at return inspection (not in pre-rental photos)
- **Calculation:** Based on repair quote (varies)
- **Cap:** Insurance deductible amount (typically €500-€1000)
- **Source:** FR-CV-007 (Vehicle return & inspection), FR-AI-003 (Pre-rental inspection)
- **Waiver:** None (actual repair costs)

**Consolidated Calculation Logic:**
```
Total Fines = min(
    Late Fee + Location Fee + Charger Fee + Cleaning Fee + No-Show Fee,
    €200  // Maximum fine cap (excluding damage fees)
) + Damage Fee (uncapped, actual cost)
```

**Business Rules:**
- **Fine Cap:** €200 total for behavioral fines (late, location, charger, cleaning, no-show)
- **Damage Separate:** Damage fees uncapped, actual repair cost charged
- **First-Time Waivers:** Applied automatically, logged for audit
- **Multiple Fines:** Can apply multiple fines in single rental (e.g., late + wrong location)
- **Dispute Window:** 48 hours to contest fines with evidence
- **Loyalty Impact:** High fine frequency may impact loyalty tier eligibility

**Fine Itemization (Receipt Example):**
```
Rental Charges:
  Base rental (180 min × €0.35/min): €63.00
  Late return (45 min × €0.50/min): €22.50
  Wrong location fee: €50.00
  Cleaning fee (Minor): €50.00 WAIVED (first offense)
  ───────────────────────────────────────────
  Subtotal: €135.50
  Tax (19% VAT): €25.75
  ───────────────────────────────────────────
  Total Charged: €161.25
```

**Evidence Collection:**
- Late return: Timestamp of return vs booked end time
- Wrong location: GPS coordinates at return, city boundary validation
- Charger blocking: Telemetry log (charge timestamps)
- Cleaning: AI-flagged photos with annotations
- No-show: Reservation start time, unlock attempt logs
- Damage: Before/after photo comparison

**Waiver Management:**
- System tracks waiver history per customer
- Waivers reset annually (e.g., one late waiver per calendar year)
- High-value customers (Gold tier): Extended waiver eligibility
- Corporate accounts: Waiver policies set by account admin

**Dispute Resolution Integration:**
- Each fine includes "Dispute This Charge" link
- Dispute submission routes to FR-CV-020 (Dispute Resolution Workflow)
- Pending disputes: Fine charged but refunded if dispute successful
- Dispute decision timeline: 3-5 business days

**Analytics & Reporting:**
- **Fine Revenue:** Total fine revenue by type, trend over time
- **Fine Frequency:** Number of fines per 1000 rentals (benchmark)
- **Waiver Usage:** Waivers applied, waiver abuse patterns
- **Dispute Rate:** Percentage of fines disputed, win/loss rate
- **Customer Impact:** Fine frequency by customer segment, churn correlation
- **Operational Insights:** High-fine locations (parking issues), high-fine times (rush hour late returns)

**Error Scenarios:**
- Fine calculation fails → Default to no fine, log for manual review
- Evidence missing (photos not uploaded) → Cannot charge fine, waive
- Payment fails for fine → Retry attempts, escalate to collections
- Disputed fine during charge → Charge anyway, refund if dispute approved
- Multiple concurrent fines > cap → Apply cap, prioritize by severity (damage > late > location > cleaning)

**Customer Communication:**
- Real-time notification: "Late return fee applies (€0.50/min after grace period)"
- Receipt: Itemized fine breakdown with evidence links
- Educational content: "How to avoid fines" tips after first offense
- Dispute option: Clear, accessible link in every fine notification

**Crew Dashboard:**
- Pending fine reviews: AI-flagged cases requiring human confirmation
- Fine approval workflow: Operations team approves/rejects AI suggestions
- Fine override: Ability to waive fees for legitimate reasons
- Analytics: Fine trends, high-fine customers, revenue tracking

**Dependencies:**
- Rental Lifecycle Service (late return detection)
- Vehicle Telemetry Service (GPS, charger blocking detection)
- AI Photo Analysis Service (cleanliness assessment)
- Payment Service (fine charges)
- Dispute Resolution Service (FR-CV-020)
- Notification Service (fine notifications, receipts)

**Related ADRs:**
- ADR-TBD: Fine cap strategy (fixed vs proportional to rental cost)
- ADR-TBD: Waiver policy automation vs manual override
- ADR-TBD: Fine prioritization logic when exceeding cap

---

### FR-CV-010: Volume-Based Pricing & Loyalty Tiers
**Business Goal:** Incentivize frequent usage, improve customer retention

**Description:** Tiered pricing where per-minute rate decreases based on monthly usage volume and customer loyalty tier.

**Source:** Business Context - Habitual Usage Features

**Acceptance Criteria:**
- [ ] System tracks customer's monthly usage (minutes consumed)
- [ ] Usage thresholds trigger automatic tier upgrades: Bronze (0-1000 min), Silver (1000-2000 min), Gold (2000+ min)
- [ ] Each tier has discounted per-minute rate: Bronze 0%, Silver 10%, Gold 15%
- [ ] Tier status visible in mobile app and booking flow
- [ ] Monthly usage resets on 1st of month
- [ ] Tier benefits: Priority booking, extended grace periods, cleaning fee waivers
- [ ] Corporate accounts: Custom tiers and rates negotiated separately

**Pricing Matrix:**
| Vehicle Type | Base Rate | Silver Rate (-10%) | Gold Rate (-15%) |
|--------------|-----------|-------------------|------------------|
| Standard Car | €0.50/min | €0.45/min        | €0.425/min       |
| Premium Car  | €0.75/min | €0.675/min       | €0.638/min       |
| Cargo Van    | €0.60/min | €0.54/min        | €0.51/min        |

**Business Rules:**
- Tier calculation: Based on previous month's usage
- Tier persistence: Maintained for current month even if usage drops
- Tier benefits apply retroactively: Upgraded mid-rental → discount applied
- Corporate accounts: Separate tier structure, pooled usage across employees

**Loyalty Tier Benefits:**
| Benefit | Bronze | Silver | Gold |
|---------|--------|--------|------|
| Discount | 0% | 10% | 15% |
| Priority Booking | No | Yes | Yes |
| Grace Period | 15 min | 30 min | 45 min |
| Cleaning Fee Waiver | No | No | First offense |
| Extension Priority | No | No | Yes |

**Dependencies:**
- Customer Profile Service (tier tracking)
- Payment Service (dynamic rate application)
- Analytics Service (usage aggregation)

---

### FR-CV-011: Corporate Account Management
**Business Goal:** B2B revenue stream, employer commute benefits

**Description:** Multi-user corporate accounts with centralized billing, usage tracking, and reporting dashboards.

**Source:** Business Context - Habitual Usage Features

**Acceptance Criteria:**
- [ ] Corporate admin creates account with company details, billing info
- [ ] Admin invites employees via email, assigns spending limits (optional)
- [ ] Employees book vehicles under corporate account
- [ ] All charges roll up to corporate billing entity
- [ ] Corporate admin dashboard shows: Usage by employee, vehicle utilization, cost breakdown
- [ ] Monthly invoicing with itemized usage report (CSV export)
- [ ] Corporate accounts have custom pricing tiers (negotiated separately)
- [ ] Admin can deactivate employee access, set booking restrictions

**Corporate Account Features:**
- Centralized billing (single invoice per month)
- Usage caps per employee (optional enforcement)
- Approval workflows for high-value bookings (vans, multi-day)
- Reporting: Usage patterns, cost allocation by department
- Tax-advantaged structuring (per EU country regulations)

**Business Rules:**
- Minimum account size: 10 employees
- Custom pricing: Negotiated based on volume commitment
- Payment terms: Net 30 for corporate accounts (vs instant for individual)
- Spending limits: Soft limits (warning) vs hard limits (booking blocked)

**Admin Dashboard Metrics:**
- Total monthly spend
- Usage by employee (top users, inactive users)
- Most booked vehicle types
- Peak usage times
- Cost per rental, cost per employee

**Dependencies:**
- Customer Management (corporate account hierarchy)
- Payment Service (B2B invoicing)
- Analytics Service (usage aggregation and reporting)

---

### FR-CV-021: Payment Method Management
**Business Goal:** Customer self-service, payment security, reduce booking failures

**Description:** Customers can add, update, remove, and set default payment methods for rentals and pre-authorization.

**Source:** Implied requirement for booking flow and payment processing

**Acceptance Criteria:**
- [ ] Customer can add payment method: Credit card, debit card (via secure tokenization)
- [ ] Card information encrypted and tokenized (PCI DSS compliance)
- [ ] Customer can store multiple payment methods
- [ ] One payment method designated as "default" for bookings
- [ ] Customer can update card details (expiry date, billing address)
- [ ] Customer can remove payment method (if not used in active bookings)
- [ ] System validates payment method before allowing bookings
- [ ] Expired cards flagged and customer notified to update
- [ ] Failed pre-authorization triggers notification to update payment method

**Payment Method Validation:**
- Card number format validation (Luhn algorithm)
- Expiry date in future
- CVV required for addition (not stored)
- $1 authorization test (immediately released) to verify card validity
- Address verification (AVS) for fraud prevention

**Business Rules:**
- Minimum one valid payment method required for booking
- Cannot remove default payment method without setting another as default
- Cannot remove payment method with pending pre-authorization
- Expired cards automatically disabled (booking requires update)
- Multiple failed validations (>3) may trigger security review

**Supported Card Types:**
- Visa
- Mastercard
- American Express
- Maestro (EU)

**PCI DSS Compliance:**
- No raw card data stored (tokens only)
- Payment gateway handles card processing (Stripe/Adyen)
- Tokenization at point of entry
- Encrypted transmission (TLS 1.3)
- Regular security audits

**User Experience:**
- Clear indication of default payment method
- Expiry warnings 30 days before expiration
- Quick card update flow from booking screen
- Support for saved browser payment credentials (Apple Pay, Google Pay)

**Error Scenarios:**
- Card validation fails → Show specific error, allow retry
- Expired card → Prompt update before booking
- Payment method removed during booking → Booking fails, prompt to add new method
- Tokenization service down → Queue request, process when available

**Corporate Accounts:**
- Corporate billing entity as payment method
- Individual employees cannot add personal cards if corporate account
- Admin controls payment method for all employees

**Dependencies:**
- Payment Gateway (Stripe/Adyen - tokenization, validation)
- Customer Management (payment method storage)
- Booking Service (payment method validation at booking)
- Notification Service (expiry warnings, validation failures)

**Related ADRs:**
- ADR-TBD: Payment gateway vendor selection (Stripe vs Adyen)
- ADR-TBD: PCI DSS compliance strategy (SAQ A vs SAQ D)
- ADR-TBD: Digital wallet integration (Apple Pay, Google Pay)

---

### FR-CV-027: Crew Mobile App Requirements
**Business Goal:** Operational efficiency, crew productivity, real-time fleet management

**Description:** Mobile app for operations crew to manage vehicle servicing, damage assessment, maintenance tasks, and fleet operations in the field.

**Source:** Operational requirement - Essential for crew productivity and operational workflows

**Acceptance Criteria:**
- [ ] Crew can authenticate with employee credentials (role-based access)
- [ ] Dashboard shows assigned tasks: Inspections, maintenance, damage assessments, relocations
- [ ] Real-time vehicle status: Location, charge level, availability, next booking
- [ ] Crew can update vehicle status (Available, Cleaning, Maintenance, Out of Service)
- [ ] Task completion workflow: Checklist, photos, notes, time tracking
- [ ] Offline mode: Tasks downloaded for offline work, synced when connected
- [ ] Navigation integration: Route to vehicle locations using Google Maps
- [ ] Push notifications for urgent tasks and priority alerts

**Core Features:**

**1. Task Management:**
- Task list view: Priority-sorted (urgent, high, normal, low)
- Task types: Post-rental inspection, damage repair, cleaning, charging, relocation, maintenance
- Task details: Vehicle info, location, required actions, estimated time, parts needed
- Task assignment: Automatically assigned based on crew location and availability
- Task tracking: Start time, completion time, actual vs estimated duration

**2. Vehicle Inspection:**
- Access inspection checklist (matches FR-CV-007 return inspection)
- Photo capture: Damage documentation, cleanliness verification, maintenance issues
- AI-assisted damage detection: Vertex AI Vision flags damage in real-time
- Cleanliness rating: 1-5 stars with notes
- Battery level verification: Compare telemetry with physical indicator
- Inspection report submission: Photos, notes, ratings uploaded to cloud

**3. Damage Assessment:**
- View customer-submitted damage reports (from FR-AI-003: Pre-Rental Inspection)
- Compare pre-rental vs post-rental photos side-by-side
- Assess damage: Severity (minor/moderate/severe), repair cost estimate, photos
- Approve or dispute customer damage claims
- Submit damage report for billing/insurance processing
- Integration with repair vendors for quotes

**4. Maintenance Operations:**
- View maintenance tasks from FR-CV-026
- Maintenance checklist: Oil change, tire rotation, brake check, etc.
- Parts inventory: Check parts availability, request parts if needed
- Log maintenance completion: Service performed, parts used, cost, next service due
- Post-maintenance inspection: Test drive checklist, safety validation
- Update vehicle status to "Active" when service complete

**5. Cleaning & Preparation:**
- Cleaning task list: Interior vacuuming, exterior wash, window cleaning, sanitization
- Cleanliness standards reference: Photos of acceptable vs unacceptable conditions
- Supply tracking: Cleaning supplies used, restocking alerts
- Special cleaning: Deep clean for high cleanliness scores (habitual users)
- Photo verification: Before/after photos for quality assurance

**6. Vehicle Relocation:**
- Rebalancing tasks: Move vehicle from low-demand to high-demand area
- Charging tasks: Drive vehicle to charging station, plug in, monitor charge progress
- Route optimization: Multi-vehicle pickup routes (batch operations)
- Fuel/charge logging: Document charge level before/after relocation
- Parking validation: Confirm vehicle in correct designated bay

**7. Fleet Overview:**
- Map view: All vehicles in crew's service area
- Filters: Status (available, in-use, maintenance), charge level, next booking time
- Vehicle details: Click vehicle → Full status, history, upcoming bookings
- Quick actions: Mark vehicle unavailable, request assistance, reassign tasks

**8. Communication:**
- Chat with dispatch/operations center
- Report urgent issues: Theft, severe damage, safety concerns
- Customer contact: Call customer for return issues or clarifications
- Handoff notes: Leave notes for next shift crew

**Business Rules:**
- Role-based access: Different features for inspectors, mechanics, cleaners, supervisors
- Task priority: Safety-critical tasks (theft, damage) take precedence
- Task SLA: Each task type has target completion time (e.g., cleaning: 30 min)
- Overtime tracking: Crew hours logged for payroll integration
- Quality assurance: Random task audits, photo review by supervisors

**Offline Capabilities:**
- Download assigned tasks at shift start (with vehicle data, maps cached)
- Capture photos and notes offline
- Sync when connectivity restored (automatic background sync)
- Conflict resolution: Server version wins if task updated by multiple crew members

**Performance Requirements:**
- Task list loads in <2 seconds
- Photo upload: Background process, doesn't block workflow
- Map updates: Real-time vehicle locations (30-second refresh)
- Battery efficient: GPS tracking only when task active

**User Experience:**
- Simple, touch-friendly interface (crew wears gloves)
- Large buttons, high contrast for outdoor visibility
- Voice input for notes (hands-free operation)
- Quick action buttons: "Start Task", "Complete Task", "Need Help"
- Dark mode for night shift operations

**Integration Points:**
- Fleet Management System (vehicle status, task assignment)
- Vertex AI Vision API (photo analysis, damage detection)
- Google Maps API (navigation, routing)
- Notification Service (push alerts for urgent tasks)
- Maintenance System (FR-CV-026 integration)
- Time Tracking / Payroll (crew hours)

**Analytics:**
- Crew productivity: Tasks completed per hour, average task duration
- Task backlog: Number of pending tasks by priority
- Quality metrics: Inspection accuracy, damage detection rate
- Utilization: Crew idle time, travel time vs task time
- Efficiency trends: Compare crew performance, identify training needs

**Error Scenarios:**
- Task assignment fails → Manual assignment via dispatch dashboard
- Photo upload fails → Retry automatically, alert if repeatedly fails
- GPS unavailable → Manual address entry, warn crew
- Vehicle not at expected location → Alert dispatch, crew searches or skips task
- Task takes longer than estimated → Auto-alert supervisor for support

**Security:**
- Employee authentication: SSO with MFA
- Session timeout: 8 hours (shift length)
- Data encryption: Photos and task data encrypted in transit and at rest
- Audit logging: All task actions logged for accountability
- Lost device: Remote wipe capability

**Dependencies:**
- Crew Management System (employee profiles, shifts, roles)
- Task Orchestration Service (task generation, assignment, tracking)
- Vehicle Status Manager (real-time vehicle data)
- Cloud Storage (photo/document storage)
- Vertex AI Vision API (AI-assisted damage detection)
- Google Maps SDK (navigation, routing)

**Related ADRs:**
- ADR-0003: Vertex AI as core platform for AI and GenAI (damage detection)
- ADR-TBD: Crew mobile app platform (native iOS/Android vs React Native)
- ADR-TBD: Offline data sync strategy (eventual consistency)
- ADR-TBD: Task assignment algorithm (location-based vs skill-based)

---

## 5. Telemetry & Monitoring

### FR-CV-012: Real-Time Telemetry Tracking
**Business Goal:** Fleet visibility, charge monitoring, theft prevention

**Description:** Vehicles transmit GPS, charge, range data every 30 seconds during active rental.

**Source:** Transcript - "GPS trackers... assume at least every 30 seconds when they're in use"

**Acceptance Criteria:**
- [ ] During active rental: Vehicle transmits telemetry every 30 seconds
- [ ] Telemetry payload: GPS coordinates, battery %, range (km), speed, timestamp
- [ ] When idle/parked: Telemetry every 5 minutes (reduced frequency)
- [ ] Telemetry ingestion pipeline handles 400 vehicles × 2 messages/min = 800 msg/min peak
- [ ] Data stored in time-series database (TimescaleDB) for historical analysis
- [ ] Real-time alerts: Low charge, speeding, geo-fence violations
- [ ] Customer app shows current location, range remaining

**Business Rules:**
- High-frequency mode: 30s intervals during rental
- Low-frequency mode: 5min intervals when idle
- Alert thresholds: Range < 50km, speed > 130 kmh, geo-fence exit

**Technical Requirements:**
- Protocol: MQTT over TLS (lightweight, efficient)
- Payload size: < 500 bytes (optimize for cellular data costs)
- Message queue: Handle bursts (all vehicles start rentals simultaneously)
- Data retention: Raw telemetry 90 days, aggregated data 7 years

**Error Scenarios:**
- Vehicle offline (no signal) → Queue messages, transmit when reconnected
- Telemetry gap > 5 minutes → Alert operations (potential theft)
- Battery critical during rental → Alert customer + roadside assistance

**Dependencies:**
- Vehicle IoT Gateway (MQTT broker)
- Telemetry Ingestion Service (message processing)
- TimescaleDB (time-series storage)
- Notification Service (real-time alerts)

**Related ADRs:**
- ADR-TBD: MQTT vs HTTP for telemetry (bandwidth, reliability)
- ADR-TBD: TimescaleDB vs InfluxDB for time-series data

---

## 6. Operations & Maintenance

### FR-CV-013: Partner Charging Location Integration
**Business Goal:** Support customer charging needs, reduce range anxiety

**Description:** Customers can view and navigate to partner charging locations across EU, with MobilityCorp coordinating payment.

**Source:** Transcript Q&A - "We've got a lot of partner locations around the EU where customers can actually top up the charge"

**Acceptance Criteria:**
- [ ] System maintains registry of partner charging locations (address, GPS, operator, pricing)
- [ ] Mobile app shows charging locations on map (filtered by distance, availability)
- [ ] Customer navigates to location (Google Maps integration)
- [ ] Customer charges vehicle, scans QR code or enters charge session ID
- [ ] MobilityCorp bills customer or pays partner directly (per partner agreement)
- [ ] Charging session logged: location, duration, kWh added, cost
- [ ] Customer receives charge completion notification

**Partner Integration Models:**
- **Model A (Direct Payment):** Customer pays partner, MobilityCorp reimburses
- **Model B (Roaming Agreement):** MobilityCorp pays partner on customer's behalf
- **Model C (Proprietary Network):** MobilityCorp-owned chargers, direct billing

**Business Rules:**
- Charging cost transparency: Shown before customer starts session
- Reimbursement: Processed within 48 hours (Model A)
- Partner SLA: 95% uptime, 4-hour fault response time

**Dependencies:**
- Mapping Service (location data, navigation)
- Payment Service (reimbursement, partner billing)
- Partner APIs (availability, session initiation)

---

### FR-CV-014: Charger Blocking Fine Avoidance & Crew Dispatch
**Business Goal:** Minimize operator fines, optimize crew operations

**Description:** System monitors charge completion and auto-dispatches crew to relocate fully-charged vehicles before blocking fines accrue.

**Source:** Business Context - Charger Blocking Fine Avoidance

**Acceptance Criteria:**
- [ ] System tracks which charging operators impose blocking fines (operator configuration)
- [ ] Telemetry monitors vehicle charge status (charging vs complete)
- [ ] When charge reaches 100% at blocking-fine operator: Trigger crew dispatch
- [ ] Crew receives task: "Relocate Vehicle X from Charger Y to Parking Z"
- [ ] Crew mobile app shows optimized route for multiple relocations
- [ ] Crew unplugs vehicle, moves to regular parking, updates status
- [ ] System logs relocation: time, crew, cost saved (avoided fine)

**Dispatch Logic:**
- Charge complete + grace period ending soon → High priority dispatch
- Multiple vehicles at same location → Batch into single crew task
- Crew availability: Route optimization considers crew current location, task queue

**Business Rules:**
- Dispatch trigger: Charge 95%+ at blocking-fine location
- Grace period buffer: Dispatch 15 minutes before fine starts
- Cost justification: Crew cost (€20) < blocking fine (€25/hour)

**Error Scenarios:**
- No crew available → Escalate to supervisor, consider accepting fine
- Vehicle already unplugged by customer → Cancel crew task
- Crew cannot access vehicle → Log issue, escalate

**Dependencies:**
- Telemetry Service (charge status monitoring)
- Crew Dispatch Service (task assignment, routing)
- Operator Registry (fine policies)

**Integration with Cleaning:**
- Crew tasks batched: "Relocate + Inspect Cleanliness + Clean if Needed"
- Route optimization: Combine charger relocation + cleaning + battery swap (for full fleet)

---

### FR-CV-015: Roadside Assistance Dispatch
**Business Goal:** Customer support, reduce abandonment rate

**Description:** Customers can report breakdowns or charge depletion emergencies, triggering roadside assistance dispatch.

**Source:** Transcript Q&A - "We do have a roadside recovery service that we can dispatch"

**Acceptance Criteria:**
- [ ] Customer reports issue via mobile app: Breakdown, charge depleted, accident, other
- [ ] System captures: Issue type, location (GPS), customer description, photos
- [ ] Incident created, assigned to nearest available roadside crew or partner service
- [ ] Customer receives ETA and crew contact info
- [ ] Crew resolves issue: Jump-start, tow to charger, on-site repair, alternative vehicle
- [ ] Incident closed, resolution logged, customer surveyed for feedback

**Issue Types:**
- **Breakdown:** Mechanical failure (won't start, flat tire, etc.)
- **Charge Depleted:** Battery empty, cannot reach charger
- **Accident:** Collision, damage (triggers insurance workflow)
- **Locked Out:** Customer locked keys in car (NFC card issue)
- **Other:** Misc issues requiring assistance

**Dispatch SLA:**
- Urban area: 30-minute ETA target
- Suburban area: 60-minute ETA target
- After-hours: Extended ETA (communicated upfront)

**Business Rules:**
- Incidents during rental: Covered at no additional cost (part of service)
- Incidents after rental ended: Customer pays for assistance
- False alarms: Logged, excessive false alarms trigger account review

**Resolution Options:**
- **On-Site Fix:** Crew resolves issue, rental continues
- **Vehicle Swap:** Crew delivers alternative vehicle, original towed back
- **Tow to Facility:** Vehicle out of service, rental terminated, prorated refund

**Dependencies:**
- Crew Dispatch Service (task assignment)
- Notification Service (customer updates)
- Partner Network (third-party roadside assistance)
- Fleet Management (vehicle swap coordination)

---

### FR-CV-026: Vehicle Maintenance Status Tracking
**Business Goal:** Fleet reliability, preventive maintenance, operational efficiency

**Description:** System tracks vehicle maintenance schedules, service history, and operational status to ensure fleet safety and availability.

**Source:** Operational requirement - Essential for fleet management at scale

**Acceptance Criteria:**
- [ ] Each vehicle has maintenance profile: Service history, scheduled maintenance, alerts
- [ ] System tracks odometer readings from telemetry (every trip)
- [ ] Automated maintenance triggers: Mileage-based (e.g., every 15,000 km), time-based (e.g., annual inspection), event-based (damage reports)
- [ ] Maintenance due alerts sent to operations team 7 days in advance
- [ ] Vehicles marked "Maintenance Required" blocked from new bookings
- [ ] Crew mobile app shows maintenance tasks and vehicle status
- [ ] Maintenance completion logged: Date, type, performed by, cost, next due date
- [ ] Post-maintenance inspection required before returning to active fleet

**Maintenance Types:**

**1. Preventive Maintenance (Scheduled):**
- **Oil change:** Every 10,000 km or 6 months
- **Tire rotation:** Every 12,000 km
- **Brake inspection:** Every 20,000 km or annually
- **Battery health check:** Annually (EVs: quarterly)
- **Annual safety inspection:** Legally required (TÜV in Germany, MOT equivalent in other countries)
- **AC/Heating service:** Annually
- **Fluid top-ups:** Coolant, brake fluid, windshield washer

**2. Corrective Maintenance (Unscheduled):**
- Damage repairs (post-rental inspection findings)
- Customer-reported issues (FR-CV-015: Roadside assistance)
- Telemetry alerts (battery degradation, sensor failures)
- Failed safety checks

**3. Regulatory Compliance:**
- Annual vehicle inspection (country-specific regulations)
- Emissions testing (if applicable)
- Insurance inspection
- Safety recalls (manufacturer notifications)

**Maintenance Status States:**
- **Active:** Available for bookings
- **Maintenance Due Soon:** Warning state, continue bookings but schedule service
- **Maintenance Required:** Block new bookings, allow current rentals to complete
- **In Maintenance:** Vehicle off-road, being serviced
- **Post-Maintenance Inspection:** Service complete, awaiting validation
- **Out of Service:** Major issues, long-term unavailable

**Automated Triggers:**
- Odometer threshold: Alert when 90% of service interval reached
- Time threshold: Alert 7 days before scheduled service date
- Telemetry anomalies: Battery health <80%, sensor malfunctions
- Damage reports: AI photo analysis flags major damage requiring repair
- Regulatory: 30-day alert before inspection expiration

**Crew Mobile App Features:**
- View vehicles requiring maintenance (filtered list)
- Maintenance task details: What needs to be done, parts required, estimated time
- Log maintenance completion: Photos, notes, parts used, cost
- Post-maintenance checklist: Test drive, safety checks, cleanliness validation
- Mark vehicle as "Ready for Service" to return to active fleet

**Business Rules:**
- Maintenance priority: Safety-critical issues (brakes, tires) take precedence
- Utilization optimization: Schedule maintenance during low-demand periods
- Batch maintenance: Group multiple vehicles at same facility to reduce downtime
- Extended maintenance: If service exceeds 24 hours, rebook affected reservations proactively
- Cost tracking: Maintenance costs per vehicle tracked for TCO (Total Cost of Ownership) analysis

**Integration with Demand Forecasting:**
- Publish maintenance events to AI forecasting system
- Forecasting model adjusts fleet availability predictions
- Helps optimize maintenance scheduling to minimize revenue impact

**Vendor Management:**
- Approved service centers: List of certified partners per city
- Service level agreements: SLA for turnaround times
- Quality assurance: Post-service inspection validates work quality
- Cost negotiation: Bulk pricing for fleet services

**Analytics & Reporting:**
- Maintenance frequency by vehicle age, model, usage patterns
- Average maintenance cost per vehicle per month
- Downtime analysis: Hours out of service, revenue impact
- Predictive maintenance: AI identifies vehicles likely to need service soon
- Compliance tracking: Ensure all vehicles meet regulatory requirements

**Error Scenarios:**
- Maintenance overdue → Block vehicle immediately, notify operations urgently
- Vehicle booked despite "Maintenance Required" status → Cancel booking, rebook customer with alternative
- Telemetry fails during rental → Manual odometer reading at return
- Service center unavailable → Route to backup facility or delay non-critical maintenance

**Customer Communication:**
- If booked vehicle needs urgent maintenance → Proactive rebooking with apology, discount/credit offered
- Maintenance transparency: "This vehicle recently serviced for your safety"
- Recurring bookings: Automatically switch to alternative vehicle during maintenance windows

**Dependencies:**
- Vehicle Telemetry Service (odometer readings, battery health)
- Fleet Management System (vehicle status, inventory)
- Crew Mobile App (maintenance task management)
- Booking Service (block/unblock availability)
- Notification Service (maintenance alerts)
- Vendor Management System (service center coordination)

**Related ADRs:**
- ADR-TBD: Maintenance scheduling algorithm (utilization optimization)
- ADR-TBD: Predictive maintenance using AI (telemetry pattern analysis)
- ADR-TBD: Service center partner selection criteria

---

### FR-CV-016: Remote Vehicle Disable
**Business Goal:** Theft prevention, security

**Description:** Operations team can remotely disable vehicle in theft or security breach scenarios.

**Source:** Transcript - "If it looks like you can steal the car... we can disable remotely"

**Acceptance Criteria:**
- [ ] Authorized operations personnel can trigger remote disable via admin dashboard
- [ ] Disable command sent to vehicle via IoT Gateway (MQTT/API)
- [ ] Vehicle responds by: Locking doors, disabling ignition, sounding alarm (optional)
- [ ] Disable action logged with: Timestamp, operator ID, reason, vehicle ID
- [ ] Customer notified (if false alarm) with apology + compensation
- [ ] Re-enable requires supervisor approval + reason

**Authorization:**
- Role required: Operations Manager or Security Team
- Two-factor authentication required for disable action
- Audit trail: All disable actions logged and reviewed monthly

**Business Rules:**
- Disable triggers: Suspected theft, overdue rental (>24h), vehicle in restricted zone
- Customer notification: Immediate SMS/email (unless confirmed theft)
- Re-enable SLA: Within 15 minutes of approval (for false alarms)

**Error Scenarios:**
- Vehicle offline → Disable queued, executes when vehicle reconnects
- False alarm → Apologize to customer, compensate with credit
- Dispute → Security team reviews GPS telemetry, rental history

**Dependencies:**
- Vehicle IoT Gateway (disable command)
- Admin Dashboard (authorization interface)
- Audit Log Service (compliance tracking)
- Notification Service (customer alerts)

**Related ADRs:**
- ADR-TBD: Security controls for remote disable (prevent abuse)
- ADR-TBD: Legal/compliance review (privacy implications)

---

**Note:** FR-CV-017 (Event Publishing for AI Demand Forecasting) has been moved to **Section 7: AI-Powered Features** as **FR-AI-002**.

---

### FR-CV-019: GDPR Compliance & Data Privacy
**Business Goal:** Legal compliance, customer trust, regulatory adherence

**Description:** System ensures GDPR compliance for all personal data processing, including right to access, rectification, erasure, and data portability.

**Source:** Transcript - "We're EU only... GDPR and other aspects that come from a regulatory point of view"

**Acceptance Criteria:**
- [ ] Customer consent collected and recorded for data processing purposes
- [ ] Privacy policy displayed and accepted during registration
- [ ] Customer can request data export (all personal data in machine-readable format)
- [ ] Customer can request data deletion ("right to be forgotten")
- [ ] Data deletion includes: Profile, rental history, photos, telemetry, payment records (per retention policy)
- [ ] Data retention policies enforced: Personal data deleted after account closure + retention period
- [ ] Anonymization: Analytics data anonymized after retention period
- [ ] Cross-border data transfer compliance (EU data sovereignty)
- [ ] Customer can update personal information and preferences
- [ ] Audit log of all data access and modifications maintained

**Data Retention Policies:**
- **Active accounts:** Data retained while account active
- **Closed accounts:** Personal data deleted after 90 days (or per legal requirement)
- **Financial records:** 7 years retention (tax/audit compliance)
- **Telemetry data:** Raw data 90 days, aggregated/anonymized data 7 years
- **Photos:** Return photos 90 days (dispute period), then deleted
- **Audit logs:** 7 years retention (compliance/security)

**Personal Data Categories:**
- Identity: Name, email, phone, address, date of birth
- Payment: Credit card tokens (not raw data), billing history
- Location: GPS coordinates from rentals, frequently visited locations
- Usage: Rental history, vehicle preferences, loyalty tier
- Photos: Customer-submitted return photos (may include faces, license plates)
- Behavioral: Booking patterns, cancellation history, driving behavior (speed, range usage)

**GDPR Rights Implementation:**
- **Right to Access:** Customer portal shows all stored personal data
- **Right to Rectification:** Customer can update profile information
- **Right to Erasure:** Account deletion triggers data removal workflow
- **Right to Portability:** Data export in JSON format within 30 days
- **Right to Object:** Customer can opt-out of marketing communications, profiling
- **Right to Restriction:** Customer can temporarily freeze account without deletion

**Data Processing Consent:**
- **Essential:** Required for service delivery (booking, payment, vehicle access)
- **Analytics:** Optional consent for usage pattern analysis, personalization
- **Marketing:** Optional consent for promotional communications
- **Profiling:** Optional consent for AI-driven recommendations, dynamic pricing

**Data Breach Response:**
- Detection of breach within 24 hours
- Notification to supervisory authority within 72 hours (if high risk)
- Notification to affected customers without undue delay
- Incident logging and post-mortem analysis

**Multi-Country Considerations:**
- Data residency: EU customer data stored in EU data centers (GCP Europe regions)
- Cross-border transfers: Only within EU, or with adequate safeguards (SCCs)
- Local regulations: Compliance with specific country requirements (e.g., Germany, France)

**Error Scenarios:**
- Data export request → Generated within 30 days, customer notified when ready
- Data deletion request → Confirmed completion within 90 days
- Consent withdrawal → Processing stopped immediately for non-essential purposes
- Data breach → Incident response protocol activated

**Dependencies:**
- Customer Management (consent tracking, profile management)
- Data Storage Services (deletion, retention enforcement)
- Audit Log Service (access tracking)
- Legal/Compliance Team (policy updates, breach notification)

**Related ADRs:**
- ADR-TBD: Data retention and anonymization strategy
- ADR-TBD: EU data residency architecture (multi-region deployment)
- ADR-TBD: Personal data encryption (at rest and in transit)

---

### FR-CV-020: Dispute Resolution Workflow
**Business Goal:** Customer satisfaction, fair conflict resolution, reduce chargebacks

**Description:** Customers can dispute charges (fines, cleaning fees) with supporting evidence. Operations team reviews and makes final decision.

**Source:** Business Context - "Customers can dispute fees with counter-evidence photos within 48 hours"

**Acceptance Criteria:**
- [ ] Customer can file dispute via mobile app within 48 hours of charge
- [ ] Dispute types: Cleaning fee, late return fine, charger connection fine, wrong location fine, damage claim
- [ ] Customer provides: Dispute reason, description, counter-evidence photos (optional)
- [ ] Dispute automatically assigned to operations team review queue
- [ ] Operations team has access to: Original photos, AI analysis, GPS logs, rental timeline
- [ ] Team can: Approve (refund charge), Deny (keep charge), Partial refund (adjust amount)
- [ ] Decision communicated to customer within 5 business days
- [ ] If approved: Refund processed within 3-5 business days
- [ ] Customer can escalate to supervisor if unsatisfied (one escalation allowed)
- [ ] All disputes logged for pattern analysis (false positive identification)

**Dispute Resolution Criteria:**
- **Cleaning Fee:** Review photo comparison, AI confidence score, crew notes
- **Late Return:** Check GPS logs, extension requests, roadside incidents
- **Charger Not Connected:** Review photo, AI analysis, customer explanation
- **Wrong Location:** Validate GPS accuracy, parking bay coordinates, geo-fence boundaries
- **Damage Claim:** Compare pre-rental vs return photos, assess liability

**Business Rules:**
- Dispute window: 48 hours from charge posting
- One dispute per charge (escalation counts as same dispute)
- Frivolous disputes (>3 denied in 6 months) may trigger account review
- Partial refunds allowed (e.g., €100 cleaning fee reduced to €50)
- Auto-approval: If AI confidence < 70% and human reviewer agrees with customer
- Pattern analysis: >5% dispute rate for specific AI model triggers retraining

**SLA Commitments:**
- Initial response: Acknowledgment within 24 hours
- Resolution decision: Within 5 business days
- Refund processing: Within 3-5 business days of approval
- Escalation review: Within 3 business days

**Customer Communication:**
- Dispute received confirmation (automated)
- Status updates if review takes >3 days
- Final decision explanation with evidence references
- Refund confirmation when processed

**Operations Team Tools:**
- Dispute dashboard: Queue, priority, aging
- Evidence viewer: Side-by-side photo comparison, AI annotations
- Decision templates: Common scenarios, recommended actions
- Supervisor escalation: One-click escalation with notes

**Error Scenarios:**
- Dispute filed after 48-hour window → Rejected automatically, exception handling available
- Missing evidence → Operations team may request additional photos from customer
- Customer doesn't respond to evidence request → Dispute auto-denied after 7 days
- Payment refund fails → Retry processing, escalate to payment team

**Analytics & Improvement:**
- Dispute rate by fine type (identify problem areas)
- AI false positive rate (cleaning assessment accuracy)
- Resolution time metrics (team performance)
- Customer satisfaction with dispute handling (post-resolution survey)

**Dependencies:**
- Payment Service (refund processing)
- Customer Management (dispute tracking, communication)
- Photo Storage Service (evidence access)
- AI Photo Analysis (confidence scores, audit trail)
- Notification Service (status updates)

**Related ADRs:**
- ADR-TBD: Dispute resolution workflow automation (rule-based vs manual)
- ADR-TBD: AI model retraining triggers based on dispute patterns

---

### FR-CV-023: Multi-Language & Multi-Currency Support
**Business Goal:** EU market expansion, localization, customer accessibility

**Description:** System supports multiple European languages and currencies for seamless cross-country operations.

**Source:** Transcript Q&A - "We could have multiple languages and we could have multiple currencies"

**Acceptance Criteria:**
- [ ] Mobile app supports EU languages: English, German, French, Spanish, Italian, Dutch, Polish
- [ ] Customer can select preferred language at registration or in settings
- [ ] All UI text, notifications, emails localized to selected language
- [ ] System supports multiple currencies: EUR (primary), GBP, PLN, SEK, DKK, CZK
- [ ] Pricing displayed in customer's preferred currency
- [ ] Currency conversion rates updated daily (via exchange rate API)
- [ ] Invoices and receipts show currency used at booking time
- [ ] Customer profile stores language and currency preferences
- [ ] Admin content management system for translation updates

**Supported Languages (Phase 1):**
- **English (EN):** Default, fallback language
- **German (DE):** Germany, Austria
- **French (FR):** France, Belgium, Luxembourg
- **Spanish (ES):** Spain
- **Italian (IT):** Italy
- **Dutch (NL):** Netherlands, Belgium
- **Polish (PL):** Poland

**Supported Currencies:**
- **EUR (€):** Primary currency, most EU countries
- **GBP (£):** Future expansion (UK)
- **PLN (zł):** Poland
- **SEK (kr):** Sweden
- **DKK (kr):** Denmark
- **CZK (Kč):** Czech Republic

**Pricing & Currency Conversion:**
- Base prices defined in EUR
- Real-time conversion to customer's preferred currency at booking
- Exchange rate locked at booking time (no fluctuation during rental)
- Conversion rate displayed transparently: "€50.00 = 217.50 PLN (rate: 4.35)"
- Refunds processed in original booking currency

**Business Rules:**
- Language selection: Defaults to device language, fallback to English
- Currency selection: Defaults to country of registration, user can override
- Translation completeness: 100% for critical flows (booking, payment, safety), 80% for secondary content
- Missing translations: Fall back to English
- Content updates: Translations synced within 24 hours of source changes

**Localization Scope:**
- Mobile app UI (all screens)
- Email notifications
- SMS messages
- Push notifications
- Legal documents (Terms of Service, Privacy Policy)
- Help center articles
- Customer support chat (multilingual agents)

**Regional Considerations:**
- Date/time formats: Local formats (DD/MM/YYYY vs MM/DD/YYYY)
- Number formats: Decimal separators (comma vs period)
- Distance units: Kilometers (km) for all EU countries
- Currency symbols: Proper placement (€50 vs 50 EUR)
- Phone number formats: International format with country codes

**Translation Management:**
- Translation keys in code (no hardcoded strings)
- Translation management platform (e.g., Lokalise, Crowdin)
- Professional translators for legal/critical content
- Machine translation for low-priority content (human review required)
- Version control for translation files

**Currency Exchange:**
- Daily rate updates from ECB (European Central Bank) or currency API
- Rate caching: 24-hour cache, fallback to last known rate if API fails
- Transaction logging: Rate used at booking stored for audit
- Regulatory compliance: Financial reporting in EUR (base currency)

**Error Scenarios:**
- Language not available → Fall back to English, notify customer
- Currency conversion API down → Use cached rates (max 48 hours old)
- Translation missing → Show English text, log for translation team
- Invalid currency selection → Default to EUR

**Analytics:**
- Track language usage by country
- Monitor translation quality (customer feedback)
- Currency preference patterns
- Impact of localization on conversion rates

**Dependencies:**
- Customer Management (language/currency preferences)
- Translation Management System (Lokalise/Crowdin)
- Currency Exchange API (ECB, XE, Fixer.io)
- Notification Service (localized messages)
- Payment Service (multi-currency processing)

**Related ADRs:**
- ADR-TBD: Translation management platform selection
- ADR-TBD: Currency exchange rate provider and caching strategy
- ADR-TBD: Localization testing strategy (automated vs manual)

---

## 7. AI-Powered Features

### FR-AI-001: Vehicle Cleanliness Verification & Cleaning Fees
**Business Goal:** Maintain brand quality, customer experience, operational efficiency

**Description:** AI-powered cleanliness assessment from return photos with human verification before fee assessment.

**Source:** Business Context - Vehicle Cleanliness Standards

**Acceptance Criteria:**
- [ ] Crew photographs vehicle before making available (baseline photos)
- [ ] AI validates baseline cleanliness meets standards
- [ ] Customer receives baseline photos at booking confirmation
- [ ] At return: Customer submits photos (exterior, interior, trunk, seats)
- [ ] AI compares return photos vs baseline for degradation
- [ ] AI flags excessive mess: stains, trash, food debris, mud, damage
- [ ] Flagged cases reviewed by operations team within 2 hours
- [ ] If confirmed dirty: Cleaning fee charged, customer notified with evidence
- [ ] Customer can dispute within 48 hours with counter-photos

**AI Assessment Criteria:**
- **Acceptable (No Fee):** Light dust, minor dirt, small marks, normal wear-and-tear
- **Minor Mess (€50):** Moderate dirt, small stains, limited trash (< 3 items)
- **Major Mess (€100):** Extensive dirt, large stains, significant trash, spills
- **Severe Mess (€150):** Heavy soiling, odors (crew confirms), damage, bio-hazards

**AI Confidence Thresholds:**
- Confidence > 90%: Auto-assess (no human review if "Acceptable")
- Confidence 70-90%: Human review required before fee
- Confidence < 70%: Inconclusive, default to "Acceptable" (benefit of customer)

**Business Rules:**
- False positive target: <5% (to maintain customer trust)
- Dispute resolution: Human review with photo comparison
- Fee waiver: First offense for loyal customers (Gold tier)
- Cleaning fee cap: €150 (severe cases beyond this → damage claim process)

**Integration with Maintenance:**
- AI-flagged vehicles → Priority cleaning dispatch
- Periodic deep cleaning: Every 10 rentals or 14 days (whichever first)
- Cleaning tasks batched with charger relocation for crew efficiency

**Error Scenarios:**
- AI system offline → Default to manual inspection (crew at next rental)
- Customer disputes fee → Human review with full photo set
- Ambiguous case (borderline) → Default to no fee, log for pattern analysis

**Dependencies:**
- AI Photo Analysis Service (Vertex AI Vision, custom trained model)
- Crew Dispatch Service (cleaning task assignment)
- Payment Service (cleaning fee charges)
- Customer Dispute Resolution (manual review queue)

**Related ADRs:**
- ADR-0003: Vertex AI as core platform for AI and GenAI
- ADR-TBD: AI model selection (Vertex AI Vision vs custom CNN)
- ADR-TBD: Training data strategy and bias mitigation
- ADR-TBD: Human-in-the-loop workflow design

**Related NFRs:**
- NFR-AI-001: AI Model Accuracy & Quality (cleanliness assessment)
- NFR-AI-002: AI Model Performance & Latency
- NFR-AI-003: AI Model Monitoring & Drift Detection
- NFR-AI-006: AI Explainability & Transparency
- NFR-AI-008: AI System Fallback & Graceful Degradation

---

### FR-AI-002: AI Demand Forecasting & Vehicle Positioning
**Business Goal:** Improve vehicle positioning, reduce "not available" scenarios

**Description:** Car/Van Rental context publishes domain events to AI Demand Forecasting system for utilization pattern analysis.

**Source:** Business Context - Predictive Vehicle Positioning

**Acceptance Criteria:**
- [ ] System publishes events: Rental Started, Rental Ended, Booking Created, Booking Cancelled
- [ ] Event payload includes: Vehicle ID, location, time, duration, customer tier, vehicle type
- [ ] Events published to message bus (Pub/Sub, Kafka) for async consumption
- [ ] Forecasting system consumes events, analyzes patterns (time, location, weather correlation)
- [ ] Forecasting system provides predictions: Expected demand by location/time
- [ ] Operations team uses predictions to proactively reposition vehicles

**Business Rules:**
- Event publishing: Near real-time (<10 second latency)
- Guaranteed delivery: At-least-once semantics (idempotent consumers)
- Event retention: 90 days in message bus

**Forecasting Use Cases:**
- Predict Monday 8am demand in financial district → Pre-position vehicles Sunday night
- Identify underutilized vehicles → Suggest repositioning or pricing adjustments
- Recurring user patterns → Reserve vehicles for standing reservations

**AI/ML Components:**
- **Training Data:** Historical booking events, weather data, local events calendar, holiday schedules
- **Features:** Time of day, day of week, location zone, vehicle type, customer tier, weather conditions
- **Model Type:** Time series forecasting (ARIMA, Prophet, LSTM)
- **Prediction Output:** Hourly demand forecast by location zone and vehicle type
- **Model Retraining:** Weekly batch retraining with latest booking patterns
- **Accuracy Target:** 75% accuracy within ±15% margin for 24-hour forecasts (NFR-AI-010)

**Crew Repositioning Workflow:**
- AI forecasts high demand in Zone A, low demand in Zone B
- System suggests moving 3 vehicles from B to A before peak hours
- Crew mobile app (FR-CV-027) shows repositioning tasks with priority
- Crew executes relocation, system tracks completion
- Post-analysis: Compare forecast vs actual demand, retrain if needed

**Dependencies:**
- Message Bus (GCP Pub/Sub or Kafka)
- AI Demand Forecasting Service (Vertex AI Forecasting consumer)
- Event Schema Registry (schema evolution)
- Crew Mobile App (FR-CV-027) for repositioning tasks
- Weather API (external data source)

**Related ADRs:**
- ADR-0003: Vertex AI as core platform for AI and GenAI
- ADR-TBD: Demand forecasting integration (batch vs streaming)
- ADR-TBD: Event schema versioning strategy
- ADR-TBD: Forecasting model selection (time-series vs ML)

**Related NFRs:**
- NFR-AI-003: AI Model Monitoring & Drift Detection
- NFR-AI-004: AI Vendor Lock-In Mitigation
- NFR-AI-010: Demand Forecasting Accuracy Requirements

---

### FR-AI-003: AI-Assisted Damage Detection (Pre-Rental Inspection)
**Business Goal:** Customer protection, dispute prevention, damage accountability

**Description:** Customer conducts mandatory vehicle condition inspection before starting rental using mobile app with AI-assisted damage detection to document pre-existing damage.

**Source:** Operational best practice - Standard rental industry practice, prevents false damage claims

**Acceptance Criteria:**
- [ ] Mobile app guides customer through inspection checklist before first use
- [ ] Customer takes photos of vehicle exterior (4 sides + roof if accessible)
- [ ] Photos uploaded to cloud storage with timestamp and GPS coordinates
- [ ] Interior inspection: Cleanliness check, seats, dashboard, cargo area
- [ ] Customer confirms: "Vehicle condition documented and acceptable"
- [ ] Pre-existing damage marked on digital vehicle diagram
- [ ] Inspection must be completed before rental session starts (vehicle stays locked until inspection done)
- [ ] Inspection report linked to rental session for return comparison
- [ ] Customer receives inspection summary via email

**Inspection Checklist:**

**Exterior Inspection:**
- [ ] Front bumper and hood (dents, scratches, paint damage)
- [ ] Driver side (doors, mirrors, windows)
- [ ] Rear bumper and trunk/cargo door
- [ ] Passenger side (doors, mirrors, windows)
- [ ] Wheels and tires (visible damage, pressure indicators)
- [ ] Lights (headlights, taillights, turn signals functional)
- [ ] Windshield (cracks, chips)
- [ ] Roof (if accessible, van skylights)

**Interior Inspection:**
- [ ] Seats (stains, tears, burns)
- [ ] Dashboard and controls (damage, missing items)
- [ ] Cargo area (clean, odor-free, dry)
- [ ] Floor mats and carpets (stains, wear)
- [ ] Seatbelts (functional, clean)
- [ ] Special equipment (child seat, bike rack if applicable)

**Photo Requirements:**
- Minimum 4 photos (front, back, left, right angles)
- Recommended 8+ photos for comprehensive documentation
- Photo quality: Minimum 1280x720 resolution
- Photos must show vehicle VIN or license plate for verification
- Timestamp and GPS embedded in EXIF metadata
- AI pre-processing: Blur detection (reject blurry photos), lighting validation

**Pre-Existing Damage Documentation:**
- Customer taps on digital vehicle diagram to mark damage locations
- For each marked area: Category (scratch/dent/crack/stain), severity (minor/moderate/severe), photo reference
- Damage visible in previous rental returns: Auto-suggested by system
- Operations team review: Damage reports validated within 1 hour (crew checks)

**Business Rules:**
- Inspection mandatory for first-time customers and new vehicle assignments
- Habitual users (5+ rentals): Optional quick inspection (express mode)
- Express mode: AI compares current photos with last inspection, flags changes
- Inspection timeout: 10 minutes to complete, or auto-prompt for assistance
- Skip option: Only if customer accepts liability for all pre-existing damage (not recommended)
- Operations override: Crew can mark vehicle as "pre-inspected" if just cleaned/checked

**AI-Assisted Damage Detection:**
- Vertex AI Vision analyzes uploaded photos for visible damage
- AI flags potential damage: "Possible dent detected on driver door, please review"
- Customer confirms or dismisses AI findings
- AI training: Learns from customer confirmations and return inspections
- Target accuracy: 80% damage detection rate, <10% false positive rate

**AI Model Specifications:**
- **Training Data:** Vehicle damage images (dents, scratches, cracks) from various angles, lighting conditions
- **Model Architecture:** Vertex AI Vision AutoML or custom object detection (YOLO, Faster R-CNN)
- **Damage Categories:** Scratch, dent, crack, paint chip, broken glass, tire damage, body panel misalignment
- **Confidence Thresholds:**
  - High confidence (>85%): Auto-flag, notify customer immediately
  - Medium confidence (70-85%): Suggest customer review, highlight region
  - Low confidence (<70%): No auto-flag, log for model improvement
- **Real-time Inference:** Process photo within 5 seconds of upload
- **Batch Processing:** Compare pre-rental vs return photos to detect new damage

**Customer Protection:**
- Inspection report is legal protection against false damage claims
- Return comparison: New damage identified by comparing pre/post photos
- Burden of proof: If damage not in pre-rental inspection, customer may be liable
- Dispute process: Customer can contest damage claims with inspection evidence

**Integration with Return Process (FR-CV-007):**
- Return inspection compares current state with pre-rental inspection
- AI identifies new damage by analyzing photo differences
- Damage assessment workflow triggered if discrepancies detected
- Links to dispute resolution (FR-CV-020) if customer contests damage charges

**Error Scenarios:**
- Photo upload fails → Allow offline storage, sync when connectivity restored
- AI service unavailable → Manual inspection only, no AI suggestions
- Customer skips inspection → Liability warning shown, acceptance logged
- Vehicle already has extensive damage → Operations team notified, vehicle may be removed from fleet

**Accessibility Considerations:**
- Voice guidance for visually impaired customers
- Haptic feedback for photo capture confirmation
- High contrast mode for damage diagram marking
- Option to request crew assistance for inspection

**Analytics:**
- Inspection completion rate and time
- Pre-existing damage frequency by vehicle age/model
- AI accuracy metrics (damage detection precision/recall)
- Correlation between thorough inspections and reduced disputes
- False positive/negative rates by damage type

**Dependencies:**
- Mobile App (inspection UI, photo capture)
- Cloud Storage (photo upload, retention)
- Vertex AI Vision API (damage detection)
- Vehicle Status Manager (pre-existing damage records)
- Rental Lifecycle Service (inspection completion validation)

**Related ADRs:**
- ADR-0003: Vertex AI as core platform for AI and GenAI (damage detection)
- ADR-TBD: Photo retention policy (GDPR compliance, storage costs)
- ADR-TBD: AI damage detection threshold tuning (accuracy vs customer friction)
- ADR-TBD: Damage detection model architecture (AutoML vs custom)

**Related NFRs:**
- NFR-AI-001: AI Model Accuracy & Quality (damage detection)
- NFR-AI-002: AI Model Performance & Latency
- NFR-AI-003: AI Model Monitoring & Drift Detection
- NFR-AI-007: AI Training Data Quality & Bias Prevention
- NFR-AI-008: AI System Fallback & Graceful Degradation
- NFR-AI-009: AI Security & Adversarial Robustness

---

## 8. Data Model Summary

**Core Entities** (conceptual overview - detailed schemas to be defined):
- **Reservation:** Booking details, pre-auth, customer, vehicle, time window, recurring patterns
- **Rental:** Active usage session, billing accumulator, telemetry reference
- **Vehicle Status:** Availability, location, charge/range, cleanliness flags, maintenance state
- **Payment Record:** Charges, fines, pre-auth hold/release, refunds, audit trail
- **Telemetry Record:** Time-series GPS, battery %, range, speed data
- **Customer Profile:** Loyalty tier, usage history, favorites, preferences
- **Corporate Account:** Company details, employees, billing, usage reports
- **Charging Session:** Partner location, kWh consumed, cost, reimbursement
- **Roadside Incident:** Issue type, location, resolution, crew assignment
- **Cleanliness Assessment:** Baseline/return photos, AI analysis, human review, fees
- **Crew Task:** Dispatch queue, task type (relocation/cleaning), route optimization

**Note:** See `4_Domain_Model.md` for ubiquitous language and aggregate boundaries. Detailed JSON schemas with field definitions, validation rules, and relationships will be defined in subsequent design phases.

---

## 9. Cross-Cutting Concerns

### Security
- Authentication: OAuth 2.0 / OpenID Connect
- Authorization: Role-based access control (RBAC)
- Data encryption: TLS in transit, AES-256 at rest
- PCI compliance: Payment data handling (tokenization)
- GDPR compliance: Data retention, right to deletion, consent management

### Observability
- Logging: Structured logs (JSON), centralized (Cloud Logging)
- Metrics: Prometheus/Cloud Monitoring (latency, throughput, errors)
- Tracing: Distributed tracing (OpenTelemetry)
- Alerting: PagerDuty integration for critical failures

### Performance
- Booking API: <500ms p95 latency
- Telemetry ingestion: 10,000 messages/second capacity
- Return photo upload: <5 seconds for 4 photos
- AI photo analysis: <30 seconds per return

### Reliability
- Availability SLA: 99.9% uptime (43 minutes downtime/month)
- Data durability: 99.999999999% (11 nines) for critical data
- Disaster recovery: RPO 1 hour, RTO 4 hours
- Multi-region deployment for EU data residency

---

## 10. Requirements Traceability Matrix

| Requirement | Requirement Name | Business Goal | Transcript Source | ADR Reference |
|-------------|------------------|---------------|-------------------|---------------|
| **Booking & Reservation Management** |
| FR-CV-001 | Advanced Booking with Charge Sufficiency | Customer Satisfaction | Q&A: "Won't let people book unless enough charge" | ADR-TBD |
| FR-CV-002 | Recurring/Standing Reservations | Habitual Usage | Business Challenge: "Use service in ad hoc way" | - |
| FR-CV-003 | Quick Rebooking & Favorites | Reduce Friction | Business Context | - |
| FR-CV-004 | Booking Extension with Conflict Checking | Flexibility | Q&A: "Can ask to extend bookings" | - |
| FR-CV-018 | Reservation Cancellation & Refunds | Customer Flexibility | Q&A: "Customers can cancel... without penalty" | ADR-TBD |
| FR-CV-024 | Booking Modification (Pre-Rental) | Customer Flexibility | Operational best practice | ADR-TBD |
| **Vehicle Access & Lifecycle** |
| FR-CV-005 | Dual Access Control (Phone NFC + Remote) | Reliable Access | Transcript: "NFC capable... API to unlock" | ADR-TBD |
| FR-CV-006 | Cross-Border with Origin City Return | Fleet Balance | Q&A: "Return to origin city" | - |
| FR-CV-022 | Late/No-Show Fee Processing | Revenue Protection | Operational requirement | ADR-TBD |
| **Return Verification & Condition Assessment** |
| FR-CV-007 | Return Verification (Parking + Charger + Photos) | Return Compliance | Transcript: "Photo as proof... plugged in" | ADR-TBD |
| **Payment & Pricing** |
| FR-CV-009 | Per-Minute Billing with Pre-Authorization | Payment Security | Q&A: "Pre-authorization... actual usage" | ADR-TBD |
| FR-CV-010 | Volume-Based Pricing & Loyalty Tiers | Revenue Growth | Business Goal: Habitual usage | - |
| FR-CV-011 | Corporate Account Management | B2B Revenue | Business Context: Corporate accounts | - |
| FR-CV-021 | Payment Method Management | Payment Security | Operational requirement | ADR-TBD |
| FR-CV-028 | Fine Calculation Engine (Consolidated) | Consistent Policy | Operational requirement | ADR-TBD |
| **Compliance & Customer Rights** |
| FR-CV-019 | GDPR Compliance & Data Privacy | Regulatory Compliance | EU operations requirement | ADR-TBD |
| FR-CV-020 | Dispute Resolution Workflow | Customer Trust | Operational requirement | ADR-TBD |
| FR-CV-023 | Multi-Language & Multi-Currency Support | EU Market Expansion | Q&A: "Multiple languages and currencies" | ADR-TBD |
| **Telemetry & Monitoring** |
| FR-CV-012 | Real-Time Telemetry Tracking | Fleet Visibility | Q&A: "GPS trackers... 30 seconds" | ADR-TBD |
| FR-CV-013 | Partner Charging Location Integration | Range Support | Q&A: "Partner locations... top up charge" | - |
| FR-CV-014 | Charger Blocking Fine Avoidance & Crew Dispatch | Cost Optimization | Business Context: Blocking fines | - |
| **Operations & Maintenance** |
| FR-CV-015 | Roadside Assistance Dispatch | Customer Support | Q&A: "Roadside recovery service" | - |
| FR-CV-016 | Remote Vehicle Disable | Security | Transcript: "Can disable remotely" | ADR-TBD |
| FR-CV-026 | Vehicle Maintenance Status Tracking | Fleet Reliability | Operational requirement | ADR-TBD |
| FR-CV-027 | Crew Mobile App Requirements | Operational Efficiency | Operational requirement | ADR-TBD |
| **AI-Powered Features** | | | | |
| **FR-AI-001** | **Vehicle Cleanliness Verification & Fees** | **Brand Quality & Efficiency** | **Business Context: Cleanliness** | **ADR-0003, ADR-TBD** |
| **FR-AI-002** | **AI Demand Forecasting & Vehicle Positioning** | **Vehicle Positioning** | **Business Challenge: "Forecast demand"** | **ADR-0003, ADR-TBD** |
| **FR-AI-003** | **AI-Assisted Damage Detection (Pre-Rental)** | **Dispute Prevention** | **Operational best practice** | **ADR-0003, ADR-TBD** |

**Note:** FR-CV-008, FR-CV-017, and FR-CV-025 have been renumbered as FR-AI-001, FR-AI-002, and FR-AI-003 respectively and moved to the AI-Powered Features section.

---

## Summary

This functional requirements document demonstrates:

1. **Clear Traceability:** Every requirement maps to business goals and transcript quotes
2. **Thoughtful AI Integration:** AI features explicitly separated (FR-AI-XXX) for clarity - cleanliness verification, damage detection, demand forecasting
3. **Trade-Off Awareness:** Human-in-the-loop for AI decisions, fallback mechanisms, confidence thresholds
4. **Operational Realism:** Crew dispatch optimization, partner integration, corporate accounts
5. **Customer-Centric:** Habitual usage features, transparent pricing, dispute resolution
6. **Bounded Context Clarity:** Clear separation from bike/scooter rental, well-defined integration points
7. **Architectural Implications:** Data models, ADR references, technical requirements included
8. **AI Governance:** Dedicated section for AI features with explicit accuracy targets, monitoring requirements, and ethical considerations

**Key Differentiators:**
- **AI-Powered Excellence:** Three distinct AI capabilities (FR-AI-001, FR-AI-002, FR-AI-003) with measurable success criteria
- Charge sufficiency validation prevents customer dissatisfaction
- Cleanliness AI with <5% false positive target maintains trust
- Damage detection AI reduces disputes and protects both customer and company
- Charger blocking fine avoidance demonstrates operational sophistication
- Corporate accounts open B2B revenue stream
- Predictive positioning leverages AI for business value (75% forecast accuracy target)

**Total Requirements: 28**
- Core Requirements (FR-CV-XXX): 25 requirements
- AI-Powered Features (FR-AI-XXX): 3 requirements