 # Extra Ideas for Future AI Use:

- AI-driven marketing & demand generation (explained in detail below);
- automatic vehicle inspection (phone scanning) for vehicle pick-up and return;
- fully automated vehicle return process with associated repair/maintenance cost calculation if applicable;
- geo-economic expansion prediction for next market expansion/acquisition;
- in-app trip-based gamification;
- automated incident detection and support;
- AI-augmented route testing;
- accessibility AI.

--------

## AI-Driven Marketing & Demand Generation

**Context**: Proactive, context-aware notifications to increase rental frequency and user engagement across all vehicle types.

### Capability Overview
**AI-powered push notification engine** that triggers personalized rental suggestions based on:
- **User behavior patterns** (ML model predicts rental likelihood from historical data)
- **Real-time context** (location, weather, traffic, time of day, local events)
- **Vehicle availability** (only trigger if vehicles available nearby)
- **Notification fatigue management** (max 2/week, opt-out, A/B test timing)

### Example Scenarios

| Vehicle Type | Trigger Conditions | Notification Example | AI/ML Component |
|--------------|--------------------|----------------------|-----------------|
| E-bike/Scooter | New city (geo-fence entry) + tourist profile | "New to Amsterdam? Explore canals by scooter! 🛴 20% first-ride discount" | User profiling ML + geo-targeting |
| E-bike/Scooter | Traffic jam + commute route (Mon-Fri 7-9am) | "Traffic jam on A10? Rent a bike and make it on time! ⏰" | Real-time traffic API + route history ML |
| Car | Recurring commute pattern (same route 5+ days) | "Same route daily? Subscription saves 30% vs pay-per-use 🚗" | Pattern detection ML + pricing optimization |
| Car | Weather forecast + historical usage | "Rain tomorrow 8am. Pre-book a car to stay dry ☔" | Weather API + user preference ML |
| Van | Weekend + home improvement store proximity | "At IKEA? Rent a van to bring furniture home! 🚚 Available 50m away" | Geo-fencing + POI detection |
| Van | Airport route history + luggage season (summer) | "Family trip next week? Van rental: €45/day, fits 7 passengers + luggage ✈️" | Calendar integration + family profile ML |
> Even more scenarios can be unlocked if not only involving user-consent information sharing device geo-location (>18 y.o.) and calendar info but also social media context and device search queries. 

### Technical Requirements (High-Level)

**Data Inputs:**
- User rental history (frequency, vehicle type, routes, time patterns)
- Real-time GPS location (with consent)
- External APIs: Weather, traffic (Google Maps), local events
- Vehicle availability from Fleet Service
- Notification response rates (open, conversion, opt-out)

**ML/AI Components:**
1. **User Propensity Model** (ML: LightGBM/XGBoost)
   - Predicts likelihood of rental in next 24h (score 0-1)
   - Features: time since last rental, day of week, weather, location
   - Retrains weekly on rental outcomes

2. **Context Matching Engine** (Rule-based + ML)
   - Matches user profile + context to notification templates
   - A/B tests message variants (discount vs urgency vs convenience)
   - Respects notification frequency caps (NFR: max 2/week)

3. **Conversion Attribution** (Analytics)
   - Tracks notification → app open → booking conversion
   - Calculates ROI per notification type
   - Feeds back to model training

**Integration Points:**
- **Fleet Service**: Check vehicle availability before triggering
- **Consumer Management**: User preferences, opt-out status, notification history
- **Price Service**: Inject dynamic discount offers
- **Mobile App**: Push notification delivery (Firebase Cloud Messaging)
- **Event Bus (Pub/Sub)**: Trigger notifications from real-time events (traffic jam detected, weather alert)

### Business Value
- **Revenue**: 15-20% increase in spontaneous rentals (industry benchmark)
- **Engagement**: Reduce 7-day dormancy rate by 25%
- **Retention**: Personalized nudges increase monthly active users
- **Competitive edge**: Proactive vs reactive rental experience

### Dependencies & Prerequisites
- **Phase 1 requirements**: User profiling, rental history analytics, real-time telemetry
- **ADR alignment**: Vertex AI (ML models), Pub/Sub (event-driven triggers), BigQuery (historical data)
- **Regulatory**: GDPR consent for location tracking, notification preferences in user profile

### Risk Mitigation
- **Privacy**: Location tracking opt-in only, anonymous aggregation for ML training
- **Spam**: Frequency caps, easy opt-out, notification quality scoring
- **Bias**: A/B test across user segments, monitor conversion equity (no demographic discrimination)

## Implementation Roadmap (Future Phases)

### Phase 2 (Post-Launch)
- Basic rule-based notifications (e.g., rain → car suggestion)
- Manual campaign management (ops team creates geo-fenced promotions)

### Phase 3 (6-12 months)
- ML propensity model (predicts rental likelihood)
- Automated A/B testing of message variants
- Notification fatigue management

### Phase 4 (12-18 months)
- Real-time context triggers (traffic, events)
- Reinforcement learning for send-time optimization
- Integration with loyalty program (tier-specific offers)

**Traceability:**
- Related to: [FR#2A (Companionship)](../2_FRs.md), [Dynamic Pricing](../hld/scenarios/dynamic-pricing), [Demand Forecasting](../hld/scenarios/demand-forecasting)
- Builds on: [ADR-0003 (Vertex AI)](../adrs/ADR-0003%20-%20Vertex%20AI%20as%20core%20platform%20for%20AI%20and%20GenAI.md), [ADR-0018 (Dynamic Pricing)](../adrs/ADR-0018%20-%20Hybrid%20AI%20for%20Dynamic%20Pricing%20calculation.md), [ADR-0022 (Demand Forecasting)](../adrs/ADR-0022%20-%20Demand%20Forecasting%20Model%20Selection%20and%20Architecture.md)

--------

# Future enhancements for core functionality:

- recurring reservations with advanced patterns (bi-weekly, specific weeks, custom schedules beyond simple daily/weekly);
- additional language support beyond Phase 1 (Romanian, Hungarian, Czech for Eastern European expansion);
- fully personalized adaptive route/experience advice with scenic routing and historical preference learning;
- gamification enhancements to loyalty program (points, badges, challenges, leaderboards for engagement);
- integration with public transit for multi-modal journey planning (seamless mobility hub);
- carbon footprint tracking and reporting (CO2 savings vs car ownership, sustainability metrics);
- peer-to-peer vehicle sharing/subletting (requires complex insurance and liability framework).



