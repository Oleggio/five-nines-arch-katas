# Car/Van Rental - Functional Requirements

## FR-CV-001: Advanced Booking
**Description:** Users must book cars/vans up to 7 days in advance for fixed durations.

**Source:** Slide deck (Booking process slide)

**Acceptance Criteria:**
- [ ] User can search availability for specific vehicle type, location, date/time
- [ ] Booking creates reservation with pre-authorization hold
- [ ] System prevents double-booking (zero tolerance)
- [ ] Reservation expires 15 minutes before start time if not claimed

**Business Rules:**
- Maximum advance booking: 7 days
- Minimum rental duration: 1 hour
- Maximum single rental: 7 days
- Pre-auth amount: Estimated total + 20% buffer + potential fines

**Data Model Reference:** See `hld/core-functionality/car-van-rental/6_Data_Models.md#Reservation`

**Dependencies:**
- Customer Management (identity verification)
- Payment Gateway (pre-authorization)
- Fleet Management (vehicle availability)

---

## FR-CV-002: Reservation Management
[Similar detailed structure...]

---

## FR-CV-003: Vehicle Access Control
**Description:** Support dual access methods - NFC card and remote unlock API.

**Source:** Slide deck (Vehicle technology and access)

**Acceptance Criteria:**
- [ ] NFC card linked to active reservation
- [ ] Mobile app can unlock via MQTT (when NFC fails)
- [ ] Unlock fails if reservation not active or expired
- [ ] Unlock event triggers rental session start

**Sequence Diagram:** See `hld/core-functionality/car-van-rental/5_Sequence_Diagrams.md#Unlock-Flow`

**Related ADR:** ADR-0003 (Synchronous vs Async Unlock)

---

[Continue for all 9+ requirements from your reqs.md...]