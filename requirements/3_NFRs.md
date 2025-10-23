# Non-Functional Requirements

## 1. Core System Qualities

**NFR_1. Scalability:** Support 10,000+ concurrent rentals across multiple EU regions with auto-scaling at 70% capacity. Handle 500 searches/sec peak load. Multi-region deployment (min 3 EU regions) with data residency compliance. 
**Risk:** Auto-scaling response lag, cross-region data replication delays.

**NFR_2. Reliability:** 99.5% uptime for critical services (booking, unlock, payment). Multi-AZ replication with point-in-time recovery (RPO ≤5 min, RTO ≤4 hours). Circuit breakers for third-party dependencies. P0 incident response ≤15 minutes. 
**Risk:** Third-party failures, cascading outages, data consistency during failover.

**NFR_3. Performance:** Booking search ≤2 seconds (p95), vehicle unlock ≤3 seconds (p99), telemetry processing ≤5 seconds. Payment pre-auth ≤5 seconds. 
**Risk:** Network latency spikes, database optimization under load, mobile performance on low-end devices.

## 2. Security & Compliance

**NFR_4. Security:** PCI DSS Level 1 compliance with tokenization. TLS 1.3 transit, AES-256 at rest. MFA for sensitive operations. GDPR breach notification ≤72 hours. API rate limiting (100 req/min/user). 
**Risk:** API abuse, credential stuffing, GDPR penalties (up to 4% revenue).

**NFR_7. Compliance:** GDPR full compliance (access, erasure, portability rights). 7-year financial audit logs. Data retention: active 3 years, financial 7 years, telemetry 30d→1y→3y, photos 90 days. Regional mobility laws and AI ethics regulations. 
**Risk:** Multi-country regulatory divergence, evolving AI regulations, audit completeness.

## 3. Operational Excellence

**NFR_5. Expandability:** Multi-region architecture for rapid EU expansion. Blue-green deployments with zero downtime. Feature flags for progressive rollout (5%→25%→100%). Rollback ≤10 minutes. Deploy ≥2×/week. 
**Risk:** Regional regulatory variations, localization testing, infrastructure delays.

**NFR_6. Localization:** 7 EU languages (EN, DE, FR, ES, IT, NL, PL) with English fallback. 6 currencies with daily exchange rates and booking-time locks. Localized formats (date, time, numbers, units). 
**Risk:** Translation quality, currency API availability, cultural UX nuances.

**NFR_7. Observability:** Comprehensive metrics, structured logs (30-day retention), distributed tracing. P0 alert acknowledgment ≤15 min. MTTD ≤5 minutes, MTTR ≤4 hours. 
**Risk:** Alert fatigue, telemetry gaps, slow detection.

**NFR_8. Cost Optimization:** Real-time cloud cost visibility. Auto-scaling policies (off-peak scale-down). Data tiering (hot→warm→cold). AI inference ≤€0.10/check. Cost-per-rental tracking. 
**Risk:** Cloud cost spiral, inefficient allocation.

## 4. AI-Specific Requirements

**NFR_9. AI Model Monitoring:** MLOps with continuous accuracy tracking and drift detection. Model accuracy targets: cleanliness 90%, damage 85%, forecasting 75-80%. Inference ≤5 seconds, cost ≤€0.10-0.15 per check. Auto-retraining if <80% accuracy for 14 days. Vendor abstraction layer (<2 week migration). Human-in-the-loop fallback (24h SLA). Explainability with confidence scores and annotations. Bias prevention through diverse datasets. Adversarial robustness (input validation, hash verification). 
**Risk:** Model drift, vendor lock-in, false positives, adversarial attacks, dataset bias.

## 5. Usability Requirements

**NFR_10. Mobile App Usability:** iOS 15+, Android 11+ support. Booking ≤3 taps from home. WCAG 2.1 Level AA accessibility. Launch ≤2 seconds. Target 4.0+ app store ratings. Offline mode for active rentals. 
**Risk:** Device fragmentation, accessibility coverage, low-end device performance.

**NFR_11. Crew App Usability:** Offline functionality with sync. Battery drain ≤10%/hour. Large touch targets (44×44 points) for field use. 12MP photo capture. One-tap navigation integration. 
**Risk:** Poor field connectivity, battery drain, outdoor visibility.