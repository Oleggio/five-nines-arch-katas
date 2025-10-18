# Car/Van Rental - Business Context

## Purpose
This document defines the business rationale and scope for the car/van rental bounded context.

## Business Goals Addressed
- **Goal**: Increase revenue through car/van segment
- **KPI**: Utilization rate > 70% (target from business goals)
- **Challenge Addressed**: Vehicle availability in right locations (from Business Challenges)

## Scope
**In Scope:**
- Advanced booking (7-day window)
- Fixed-duration rentals (no ad-hoc minute-based like bikes)
- Return verification with photo proof
- Per-minute billing with fines

**Out of Scope:**
- Subscription models (future scope - Appendix C)
- Long-term leasing (>30 days)
- Cross-border rentals without return to origin country

## Differentiators vs Bike/Scooter Rental
| Aspect | Car/Van | Bike/Scooter |
|--------|---------|--------------|
| Booking | Required (7-day advance) | Ad-hoc preferred |
| Duration | Fixed reservation slots | Open-ended |
| Return | Designated bays + charger | Flexible zones |
| Pricing | Per-minute + fines | Per-minute only |
| Access | NFC card + app unlock | App only |

## Source Traceability
- Slide deck: "Car and van rental requires advanced booking"
- Slide deck: "Return requirements" (parking bay + EV charger)
- Transcript 00:25:12: "Cars have fixed duration, bikes don't"