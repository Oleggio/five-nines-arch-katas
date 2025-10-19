# Car/Van Rental - Business Context

## Purpose
This document defines the business rationale and scope for the car/van rental bounded context.

## Business Goals Addressed
- **Goal**: Increase revenue through car/van segment
- **KPI**: Utilization rate > 70% (target from business goals)
- **Challenge Addressed**: Vehicle availability in right locations (from Business Challenges)
- **Customer Satisfaction**: Prevent charge-related incidents through proactive monitoring
- **Cost Optimization**: Minimize charging operator fines through intelligent maintenance dispatch
- **Brand Quality**: Maintain vehicle cleanliness standards to ensure positive customer experience
- **Operational Efficiency**: AI-powered cleanliness validation reduces manual inspection overhead

## Scope
**In Scope:**
- Advanced booking (7-day window) with charge sufficiency validation
- Fixed-duration rentals with extension capability (subject to availability)
- Return verification with photo proof and EV charger connection validation
- Vehicle cleanliness verification (exterior + interior) via AI-powered photo analysis
- Cleaning fee assessment for excessive mess/damage
- Per-minute billing with fines (late return, improper charging, cleaning, etc.)
- Cross-border rentals with origin city return constraint
- Partner charging network integration across EU
- Roadside recovery service for breakdowns and charge depletion
- Maintenance crew dispatch for charger blocking fine avoidance and cleaning operations
- Remote vehicle disable for security/theft scenarios
- **Recurring/Standing reservations** for habitual usage (e.g., every Monday 8am-6pm)
- **Quick rebooking** and favorite vehicle/route preferences
- **Volume-based pricing discounts** and loyalty tier management
- **Predictive vehicle positioning** using AI demand forecasting
- **Corporate account management** with usage tracking and reporting

**Out of Scope:**
- Subscription models (future scope - Appendix C)
- Long-term leasing (>30 days)
- Cross-border rentals with return to different country (must return to origin city)
- **Home/work parking partnerships** (requires business development, real estate negotiations)
- **Gamification/loyalty rewards programs** (marketing initiative, not architectural dependency)
- **Cross-mode journey planning** (requires bike/scooter context integration - separate bounded context)
- **Weekly passes or unlimited usage packages** (requires new business model approval)
- **Family/corporate shared minute pools** (complex billing model, future consideration)
- **Multi-stop rental support** (complicates duration pricing, return verification)
- **Flexible return locations (one-way rentals)** (creates fleet imbalance, contradicts origin city rule)
- **Cross-border commute whitelist exceptions** (requires bilateral regulatory agreements)
- **Behavioral nudges via push notifications** (marketing feature, can use existing notification service)

## Differentiators vs Bike/Scooter Rental
| Aspect | Car/Van | Bike/Scooter |
|--------|---------|--------------|
| Booking | Required (7-day advance) | Ad-hoc preferred |
| Duration | Fixed reservation slots (extendable) | Open-ended (max 12 hours) |
| Return | Designated bays + EV charger mandatory | Flexible zones |
| Pricing | Per-minute + multiple fine types | Per-minute only |
| Access | NFC card + app unlock | App only |
| Charge Management | Pre-booking validation, partner network | Swap-based (crew handles) |
| Recovery Service | Roadside assistance available | Limited support |

## Core Business Rules

### Charge Management
**Insufficient Charge Prevention**: Vehicles with insufficient battery range for the requested rental duration CANNOT be booked. This prevents customer dissatisfaction and reduces roadside assistance costs.

**Partner Charging Network**: MobilityCorp maintains partnerships with charging operators across the EU. Customers can top up vehicle charge at partner locations, with MobilityCorp handling payment coordination on their behalf.

**Charger Blocking Fine Avoidance**: Some charging operators impose fines when fully-charged vehicles remain connected, blocking charging spots. The system must:
- Track which charging operators have blocking fines enabled
- Monitor vehicle charge completion status
- Automatically dispatch maintenance crews to relocate fully-charged vehicles to regular parking
- Prevent unnecessary operator fines that erode profitability

### Cross-Border Operations
**Origin City Return Constraint**: Vehicles taken cross-border (e.g., Berlin to Amsterdam) must be returned to designated parking bays in the **origin city** only. This prevents fleet imbalance across countries and simplifies regulatory compliance.

### Return Process
**EV Charger Connection Mandatory**: Upon return, vehicles must be:
1. Parked in designated bay
2. Connected to EV charger (verified via AI photo analysis)
3. Photo proof submitted showing proper connection

Failure to properly connect charger results in customer fine.

### Customer Support
**Roadside Recovery Service**: For breakdowns or unexpected charge depletion, MobilityCorp dispatches roadside assistance to help customers. This service differentiates car/van rental from the bike/scooter segment.

### Security
**Remote Disable Capability**: In theft or security breach scenarios, operations team can remotely disable vehicles. This capability requires strict authorization and audit trails.

### Rental Extensions
**Extension Approval Workflow**: Customers may request to extend their rental. System checks for conflicting next bookings before approving extensions, ensuring no double-booking occurs.

### Habitual Usage Features (Addressing "Use Us Daily" Business Goal)

**Recurring/Standing Reservations**: 
- Customers can create recurring booking patterns (e.g., "every Monday 8am-6pm", "weekdays 7-9am")
- System pre-books vehicles based on patterns, ensuring availability
- Customer can skip individual occurrences without penalty
- Drives predictable revenue and enables better fleet positioning

**Quick Rebooking & Favorites**:
- One-tap rebooking of previous trips
- Save favorite vehicles, routes, time slots
- Smart defaults based on usage history
- Reduces booking friction for repeat customers

**Volume-Based Pricing & Loyalty Tiers**:
- Per-minute rate decreases based on monthly usage volume (e.g., 10% off after 20 hours/month)
- Loyalty tiers (Bronze/Silver/Gold) with progressively better rates
- Priority booking access during high-demand periods for frequent users
- Makes frequent usage economically attractive vs occasional use

**Predictive Vehicle Positioning**:
- AI demand forecasting identifies regular commute patterns
- Proactively position vehicles at high-demand locations before peak hours
- Reduces "vehicle not available" frustration for habitual users
- Integrates with existing demand forecasting system (already planned)

**Corporate Account Management**:
- B2B accounts with multiple users under single billing entity
- Usage tracking and reporting dashboards for employers
- Potential for employer subsidies/commute benefits
- Separate pricing models for corporate vs individual customers

**Rationale for Inclusion**: These features directly address the business goal "we want our customers to use our services more frequently" and can be implemented within the car/van rental bounded context without requiring extensive external negotiations or cross-context dependencies.

### Vehicle Cleanliness Standards
**"Mutual Care" Policy**: Customers receive vehicles in clean condition and must return them similarly. This shared responsibility model balances customer accountability with operational realities, similar to approaches used by leading car-sharing services (Zipcar, ShareNow) and traditional rental companies.

**Pre-Rental Guarantee**: 
- Maintenance crew photographs vehicle exterior/interior before making available for booking
- AI validates cleanliness meets MobilityCorp standards (no visible stains, trash, significant dirt)
- Customer receives photographic proof of clean vehicle condition at pickup
- Sets clear baseline for return expectations

**Post-Rental Verification**:
- Customer submits return photos (exterior, interior, trunk, seats) as part of return process
- AI analyzes photos for:
  - **Exterior**: Excessive dirt/mud, scratches, dents, damage
  - **Interior**: Stains, trash, spills, food debris, excessive dirt
  - **Trunk/Seats**: Damage, cargo residue, excessive wear beyond normal use
- Reasonable wear-and-tear is **explicitly acceptable** (minor dust, small marks, light dirt)
- **Excessive mess** triggers cleaning fee assessment (€50-150 based on severity level)
- Thresholds calibrated to industry standards to prevent customer dissatisfaction

**AI-Enhanced, Human-Verified Model**:
- AI provides **first-pass screening** for scalability and efficiency (processes 100% of returns)
- Flagged cases reviewed by **crew/operations team** before fee assessment (prevents false charges)
- Customers can **dispute fees** with counter-evidence photos within 48 hours
- Operations team makes final billing decision with photographic evidence trail
- Target: <5% false positive rate to maintain customer trust and brand reputation

**Integration with Maintenance Operations**:
- Periodic deep cleaning scheduled regardless of condition (every 10 rentals or 2 weeks)
- AI-flagged vehicles prioritized for immediate crew cleaning dispatch
- Cleaning tasks batched with charger relocation and maintenance operations for efficiency
- Crew mobile app shows combined tasks (charging → cleaning → relocation) for route optimization

**Competitive Benchmark**: 
- Industry standard cleaning fees: €25-€200 (varies by region and company)
- Traditional rental: Manual inspection at return (high labor cost)
- Car-sharing: Customer self-reporting with retroactive fees (dispute-prone)
- MobilityCorp differentiation: AI-powered, transparent, evidence-based approach reduces disputes and scales efficiently

## Out of Scope - Rationale

### Features Requiring Business/Legal Negotiations
**Home/Work Parking Partnerships**: While valuable, this requires real estate negotiations, landlord agreements, and physical infrastructure setup. This is a business development initiative, not an architectural concern. Architecture can support partner parking locations if/when business secures them.

**Cross-Border Commute Whitelist Exceptions**: Allowing specific cross-border routes (e.g., Amsterdam ↔ Brussels returns) requires bilateral regulatory agreements, insurance adjustments, and fleet rebalancing strategies. Too much business complexity for initial architecture scope.

### Features Outside Bounded Context
**Cross-Mode Journey Planning**: Requires integration with bike/scooter rental context. This should be handled at a higher-level "Trip Planning" service that orchestrates across multiple rental types. Car/van rental should expose APIs for consumption, not implement cross-mode logic.

**Behavioral Nudges via Push Notifications**: This is a marketing/engagement feature that can leverage existing notification infrastructure. Not a core rental workflow requirement. Marketing team can use published events to trigger campaigns.

**Gamification/Loyalty Rewards Programs**: Marketing initiative focused on engagement mechanics (badges, leaderboards, social features). While pricing/discounts are in scope, gamification UX is not an architectural dependency for rental operations.

### Features Requiring Business Model Changes
**Weekly Passes or Unlimited Usage Packages**: Fundamentally changes pricing model from per-minute to subscription-based. Transcript explicitly states "focus on pay-as-you-go, not subscriptions" unless it "fundamentally changes architectural approach." Excluded per business guidance.

**Family/Corporate Shared Minute Pools**: Complex billing model where multiple users draw from shared allowance. Requires sophisticated usage tracking, pool management, and conflict resolution. Too complex for initial scope; standard corporate accounts (separate billing per user) are sufficient.

**Flexible Return Locations (One-Way Rentals)**: Contradicts explicit business rule "must return to origin city." Creates fleet imbalance problems that demand forecasting is trying to solve. Architecturally possible but explicitly prohibited by business constraints.

**Multi-Stop Rental Support**: Complicates duration-based pricing (do you pause billing at stops?), return verification (how do you know final return?), and charge validation. Adds significant complexity for unclear business value in car/van segment.

## Operational Context

### Fleet Scale (per EU country)
- ~200 cars
- ~200 vans
- Target utilization: >70%
- Focus on **improving utilization** of existing fleet before adding vehicles (environmental and cost considerations)

### Maintenance Operations
**Proactive Charger Management**: 
- Monitor charging status in real-time
- Predict charge completion times
- Schedule crew dispatch to prevent blocking fines
- Optimize crew routes for multiple vehicle relocations

**Proactive Cleanliness Management**:
- AI analysis of return photos for immediate cleanliness assessment
- Automatic crew dispatch for flagged vehicles requiring cleaning
- Scheduled periodic deep cleaning (every 10 rentals or 2 weeks)
- Route optimization combining charging, cleaning, and relocation tasks

**Reactive Support**:
- Respond to customer breakdown reports
- Coordinate with partner charging locations
- Handle charge depletion emergencies
- Process customer cleanliness disputes with evidence review

## Source Traceability
- Slide deck: "Car and van rental requires advanced booking"
- Slide deck: "Return requirements" (parking bay + EV charger)
- Transcript 00:25:12: "Cars have fixed duration, bikes don't"
- Transcript: "Won't let people book a car/van unless it's got enough charge"
- Transcript: "Partner locations around the EU where customers can top up charge"
- Transcript: "Roadside recovery service that we can dispatch"
- Transcript: "200 cars, 200 vans per EU country"
- Transcript: "Cross-border must return to origin city"