# Car/Van Rental - Non-Functional Requirements

## Overview
This document defines the non-functional requirements (quality attributes) for the Car/Van Rental bounded context. These requirements describe HOW the system should behave, complementing the functional requirements that describe WHAT the system should do.

**Related Documents:**
- Business Context: `1_Business_Context.md`
- Functional Requirements: `2_FRs.md`
- Domain Model: `4_Domain_Model.md`
- Integration Requirements: `5_Integration_Requirements.md`

---

## 1. Performance Requirements

### NFR-CV-001: Booking Response Time
**Category:** Performance  
**Priority:** HIGH

**Requirement:** Booking search and reservation creation must complete within acceptable timeframes to prevent customer abandonment.

**Acceptance Criteria:**
- Availability search query: ≤ 2 seconds (95th percentile)
- Reservation creation: ≤ 3 seconds (95th percentile)
- Extension request processing: ≤ 2 seconds (95th percentile)
- Quick rebooking (favorites): ≤ 1 second (95th percentile)

**Rationale:** Industry standard for e-commerce checkout experiences. Longer delays result in cart abandonment (~40% increase per additional second after 3s).

**Measurement:** Application Performance Monitoring (APM) tools tracking end-to-end latency from mobile app to backend response.

**Source:** E-commerce UX best practices, mobile app usability standards

---

### NFR-CV-002: Vehicle Unlock Latency
**Category:** Performance  
**Priority:** HIGH

**Requirement:** Vehicle unlock command must execute rapidly to avoid customer frustration at vehicle location.

**Acceptance Criteria:**
- NFC tap to unlock confirmation: ≤ 3 seconds (99th percentile)
- Includes: NFC authentication, backend authorization, vehicle API call, mechanical unlock
- Fallback to manual unlock code if exceeds 10 seconds

**Rationale:** Customer is standing at vehicle expecting immediate access. Delays >5 seconds perceived as system failure.

**Measurement:** End-to-end telemetry from mobile app NFC event to vehicle unlock confirmation.

**Source:** Transcript - "customers can remotely lock and unlock those vehicles"

---

### NFR-CV-003: Real-Time Telemetry Processing
**Category:** Performance  
**Priority:** MEDIUM

**Requirement:** Vehicle telemetry must be processed in near real-time for monitoring, safety, and operational purposes.

**Acceptance Criteria:**
- GPS telemetry ingestion: Every 30 seconds during active rental
- Telemetry processing delay: ≤ 5 seconds from vehicle transmission to dashboard visibility
- Charge level updates: ≤ 60 seconds for crew mobile app display
- Geo-fence violation detection: ≤ 10 seconds

**Rationale:** Real-time monitoring enables proactive incident response, theft detection, and operational efficiency.

**Measurement:** Time-series database ingestion lag, stream processing latency metrics.

**Source:** Transcript Q&A - "assume at least every 30 seconds when they're in use"

---

### NFR-CV-004: Payment Processing Speed
**Category:** Performance  
**Priority:** MEDIUM

**Requirement:** Payment operations must complete quickly to enable seamless rental completion.

**Acceptance Criteria:**
- Pre-authorization: ≤ 5 seconds (synchronous)
- Final charge settlement: ≤ 10 seconds at rental end
- Refund processing: ≤ 3 business days
- Multi-currency conversion: ≤ 2 seconds

**Rationale:** Customers expect instant rental completion. Payment delays create perception of system issues.

**Measurement:** Payment gateway API response times, transaction completion rates.

**Source:** Transcript - "We do pre-authorization on a customer's credit card or debit card"

---

## 2. Scalability Requirements

### NFR-CV-005: Concurrent User Support
**Category:** Scalability  
**Priority:** HIGH

**Requirement:** System must support peak concurrent usage patterns across EU countries.

**Acceptance Criteria:**
- Support 10,000 concurrent active rentals (EU-wide)
- Peak booking load: 500 searches per second (morning commute rush)
- Graceful degradation: Maintain core booking/unlock functionality under 2x expected load
- Auto-scaling: Scale out within 2 minutes of 70% capacity threshold

**Calculation Basis:**
- Per country: 200 cars + 200 vans = 400 vehicles
- Average utilization: 70% target → 280 concurrent rentals per country
- ~25 EU countries (subset) → ~7,000 concurrent, rounded to 10,000 with buffer

**Measurement:** Load testing results, production metrics during peak hours (Mon-Fri 7-9am, 5-7pm).

**Source:** Business Context - "Utilization rate > 70% (target from business goals)"

---

### NFR-CV-006: Data Volume Handling
**Category:** Scalability  
**Priority:** MEDIUM

**Requirement:** System must efficiently handle growing telemetry and transactional data volumes.

**Acceptance Criteria:**
- Telemetry ingestion: 400 vehicles × 30-second intervals = ~1,200 events/min per country
- Retention: 30 days hot storage (time-series), 3 years cold archive
- Query performance: Historical data queries ≤ 10 seconds for 90-day range
- Photo storage: Support 10,000 photos/day (return verification + cleanliness)

**Rationale:** GPS telemetry, photos, and transaction logs accumulate rapidly. Must remain queryable for analytics and compliance.

**Measurement:** Database write throughput, storage costs, query performance dashboards.

**Source:** FR-CV-012 (Telemetry), FR-CV-019 (GDPR 3-7 year retention)

---

### NFR-CV-007: Geographic Distribution
**Category:** Scalability  
**Priority:** MEDIUM

**Requirement:** Architecture must support multi-country deployment within EU with data sovereignty compliance.

**Acceptance Criteria:**
- Deploy services in minimum 3 EU regions (e.g., Western, Central, Northern Europe)
- Data residency: Customer PII stored in country/region of registration per GDPR
- Cross-border rental data: Transaction metadata replicated, PII retained in origin country
- Regional failover: ≤ 5 minutes to redirect traffic if region fails

**Rationale:** EU GDPR data sovereignty, latency optimization, disaster recovery.

**Measurement:** Multi-region deployment manifests, data residency audit logs.

**Source:** Transcript - "let's assume that you're EU only", GDPR compliance requirements

---

## 3. Availability & Reliability Requirements

### NFR-CV-008: Service Availability SLA
**Category:** Availability  
**Priority:** HIGH

**Requirement:** Core booking and vehicle access services must maintain high availability to prevent revenue loss and customer dissatisfaction.

**Acceptance Criteria:**
- **Critical Services** (Booking, Unlock, Payment): 99.5% uptime (≤ 3.6 hours downtime/month)
- **Supporting Services** (Notifications, Analytics): 99.0% uptime
- Planned maintenance windows: Off-peak hours only (2-6am local time, max 4 hours/month)
- Incident response: P0 incidents acknowledged ≤ 15 minutes, resolved ≤ 4 hours

**Rationale:** Revenue-generating services must be highly available. Customer at vehicle expecting unlock cannot tolerate outages.

**Measurement:** Uptime monitoring, incident management system (PagerDuty, Opsgenie).

**Source:** Industry standard for e-commerce/SaaS services, mobile-first customer expectations

---

### NFR-CV-009: Data Durability & Backup
**Category:** Reliability  
**Priority:** HIGH

**Requirement:** Financial and booking data must be protected against data loss.

**Acceptance Criteria:**
- Database replication: Multi-AZ synchronous replication (RPO = 0)
- Backup frequency: Continuous backups with point-in-time recovery (PITR)
- Backup retention: 30 days for operational recovery, 7 years for compliance (GDPR, tax)
- Recovery Time Objective (RTO): ≤ 4 hours for complete database restoration
- Recovery Point Objective (RPO): ≤ 5 minutes (acceptable data loss window)

**Rationale:** Financial transactions and customer data are business-critical assets. Regulatory compliance mandates retention.

**Measurement:** Disaster recovery drills (quarterly), backup restoration tests, audit logs.

**Source:** FR-CV-019 (GDPR retention), payment industry standards (PCI DSS)

---

### NFR-CV-010: Fault Tolerance & Resilience
**Category:** Reliability  
**Priority:** MEDIUM

**Requirement:** System must gracefully handle partial failures without complete service disruption.

**Acceptance Criteria:**
- Circuit breaker pattern: Prevent cascading failures from third-party dependencies (payment gateway, mapping services)
- Retry logic: Exponential backoff with jitter for transient failures (max 3 retries)
- Fallback mechanisms:
  - Payment gateway failure → Queue for retry, allow unlock with manual review
  - AI photo verification failure → Human crew review workflow
  - Mapping service failure → Use cached route data
- Bulkhead isolation: Critical services isolated to prevent resource exhaustion

**Rationale:** Third-party dependencies (payment, AI, maps) can fail. System must degrade gracefully rather than full outage.

**Measurement:** Fault injection testing (chaos engineering), dependency failure simulation.

**Source:** Transcript - "if you can make the business case for third party services", AI uncertainty discussions

---

## 4. Security Requirements

### NFR-CV-011: Authentication & Authorization
**Category:** Security  
**Priority:** HIGH

**Requirement:** Secure customer identity verification and access control for vehicle operations.

**Acceptance Criteria:**
- Multi-factor authentication (MFA): Required for account creation and payment method changes
- Session management: JWT tokens with 30-minute expiry, refresh tokens valid 7 days
- Role-based access control (RBAC):
  - Customer: Book, unlock, pay
  - Crew: View tasks, update vehicle status, submit inspections
  - Operations: Remote disable, dispute resolution, fine adjustments
  - Admin: User management, system configuration
- NFC security: Encrypted challenge-response protocol for vehicle unlock

**Rationale:** Prevent unauthorized vehicle access, protect customer financial data, enable audit trails.

**Measurement:** Security audit logs, penetration testing results, authentication failure rates.

**Source:** Transcript - "customers have a mobile device that is NFC capable", vehicle remote disable capability

---

### NFR-CV-012: Payment Data Security (PCI DSS)
**Category:** Security  
**Priority:** HIGH

**Requirement:** Comply with PCI DSS standards for payment card data handling.

**Acceptance Criteria:**
- Tokenization: Store payment tokens only, no raw card numbers in application databases
- TLS 1.3: All payment data transmission encrypted in transit
- Encryption at rest: Database-level encryption for sensitive customer data (AES-256)
- Third-party gateway: Use PCI DSS Level 1 certified payment processor (Stripe, Adyen, Braintree)
- Audit logging: All payment operations logged with immutable audit trail

**Rationale:** Legal requirement for handling payment card data. Reduces liability in case of breach.

**Measurement:** Annual PCI DSS compliance audit, quarterly vulnerability scans.

**Source:** FR-CV-009 (Payment processing), FR-CV-021 (Payment method management)

---

### NFR-CV-013: Data Privacy (GDPR Compliance)
**Category:** Security  
**Priority:** HIGH

**Requirement:** Comply with EU GDPR for customer personal data protection.

**Acceptance Criteria:**
- Data minimization: Collect only necessary data for service provision
- Consent management: Explicit opt-in for marketing communications
- Right to access: Provide customer data export ≤ 30 days
- Right to erasure: Complete data deletion ≤ 30 days (except legal retention requirements)
- Data portability: Export customer data in machine-readable format (JSON/CSV)
- Breach notification: Report to authorities ≤ 72 hours, notify customers if high risk

**Rationale:** Legal requirement for EU operations. Non-compliance results in fines up to 4% of annual revenue.

**Measurement:** GDPR compliance audits, data subject access request (DSAR) response times.

**Source:** FR-CV-019 (GDPR Compliance), Transcript - "GDPR compliance required"

---

### NFR-CV-014: API Security
**Category:** Security  
**Priority:** MEDIUM

**Requirement:** Protect backend APIs from abuse and unauthorized access.

**Acceptance Criteria:**
- Rate limiting: 100 requests/minute per user, 1,000 requests/minute per IP
- API authentication: OAuth 2.0 tokens for mobile app, API keys for crew app
- Input validation: Sanitize all user inputs to prevent injection attacks
- CORS policies: Restrict API access to authorized domains only
- DDoS protection: CloudFlare or AWS Shield integration

**Rationale:** Prevent API abuse, protect against credential stuffing and brute force attacks.

**Measurement:** API gateway metrics, security event logs, blocked request counts.

**Source:** General security best practices for mobile-first applications

---

## 5. Usability & Accessibility Requirements

### NFR-CV-015: Mobile App Usability
**Category:** Usability  
**Priority:** HIGH

**Requirement:** Mobile application must provide intuitive, frictionless user experience.

**Acceptance Criteria:**
- Platform support: iOS 15+, Android 11+ (covers 95% of EU smartphone users)
- One-tap booking: Favorites/quick rebooking ≤ 3 taps from home screen
- Offline mode: Display active rental details, vehicle location (cached) when offline
- Accessibility: WCAG 2.1 Level AA compliance (screen readers, color contrast, font scaling)
- Load time: App launch ≤ 2 seconds on mid-tier devices (Snapdragon 6-series equivalent)

**Rationale:** Mobile app is primary customer interface. Poor UX drives churn to competitors.

**Measurement:** App store ratings (target 4.0+), user session analytics, accessibility audits.

**Source:** FR-CV-003 (Quick rebooking), mobile-first business model

---

### NFR-CV-016: Multi-Language Support
**Category:** Usability  
**Priority:** MEDIUM

**Requirement:** Application must support major EU languages for broad market reach.

**Acceptance Criteria:**
- Languages: EN, DE, FR, ES, IT, NL, PL (covers 80% of EU population)
- Dynamic language switching: No app restart required
- Localized content: Currency, date/time formats, measurement units
- Translation quality: Professional translation, not machine-generated
- Fallback: English if requested language unavailable

**Rationale:** Multi-language support increases addressable market and customer satisfaction in EU.

**Measurement:** Language usage analytics, customer support tickets related to language issues.

**Source:** FR-CV-023 (Multi-Language & Multi-Currency), Transcript - "multiple languages and currencies"

---

### NFR-CV-017: Crew Mobile App Usability
**Category:** Usability  
**Priority:** MEDIUM

**Requirement:** Crew mobile app must support efficient field operations.

**Acceptance Criteria:**
- Offline functionality: View tasks, capture photos, update status while offline (sync when connected)
- Battery optimization: ≤ 10% battery drain per hour of active use
- Large touch targets: Minimum 44×44 points for field use (gloves, outdoor conditions)
- Photo capture: Support high-resolution (12MP) photos with compression for upload
- Navigation integration: One-tap launch to Google Maps/Apple Maps for routing

**Rationale:** Crew works outdoors, often in areas with poor connectivity. App must be ruggedized for field use.

**Measurement:** Crew feedback surveys, task completion rates, offline mode usage analytics.

**Source:** FR-CV-027 (Crew Mobile App Requirements)

---

## 6. Operational Requirements

### NFR-CV-018: Monitoring & Observability
**Category:** Operational  
**Priority:** HIGH

**Requirement:** Comprehensive monitoring to detect issues before customer impact.

**Acceptance Criteria:**
- **Metrics:** CPU, memory, disk, network, request rates, error rates, latency (p50, p95, p99)
- **Logging:** Structured logs (JSON), centralized log aggregation (ELK/Splunk), 30-day retention
- **Tracing:** Distributed tracing for request flows across services (OpenTelemetry)
- **Alerting:**
  - P0 alerts: Critical service down, payment failures >5%
  - P1 alerts: Performance degradation, error rate >1%
  - On-call rotation: 24/7 coverage with ≤ 15 minute acknowledgment SLA
- **Dashboards:** Real-time operations dashboard for booking rate, active rentals, fleet status

**Rationale:** Proactive issue detection reduces customer impact and enables data-driven optimization.

**Measurement:** Mean Time to Detect (MTTD) ≤ 5 minutes, Mean Time to Resolve (MTTR) ≤ 4 hours.

**Source:** Industry DevOps best practices, SRE principles

---

### NFR-CV-019: Deployment & Release Management
**Category:** Operational  
**Priority:** MEDIUM

**Requirement:** Enable rapid, safe deployments with minimal customer disruption.

**Acceptance Criteria:**
- Deployment frequency: ≥ 2 deployments per week (support continuous delivery)
- Blue-green deployments: Zero-downtime releases for critical services
- Feature flags: Progressive rollout (5% → 25% → 100% over 48 hours)
- Rollback capability: ≤ 10 minutes to revert to previous version if issues detected
- Automated testing: 80% code coverage, ≤ 10 minute CI/CD pipeline execution

**Rationale:** Frequent deployments enable rapid iteration and bug fixes. Safety mechanisms prevent widespread customer impact.

**Measurement:** Deployment frequency, rollback rate, pipeline success rate, change failure rate.

**Source:** DevOps best practices, continuous delivery principles

---

### NFR-CV-020: Cost Optimization
**Category:** Operational  
**Priority:** MEDIUM

**Requirement:** Architecture must be cost-efficient to support business profitability.

**Acceptance Criteria:**
- Cloud cost monitoring: Real-time visibility into per-service costs
- Auto-scaling policies: Scale down during off-peak hours (2-6am local time)
- Spot/preemptible instances: Use for non-critical batch workloads (demand forecasting, analytics)
- Data storage tiering: Hot (30 days) → Warm (1 year) → Cold archive (3-7 years)
- Third-party cost management:
  - AI inference costs: ≤ €0.10 per photo analysis
  - Payment gateway fees: ≤ 2.9% + €0.25 per transaction
  - Mapping API calls: Cached routes, batch geocoding

**Rationale:** Cloud costs can spiral. Cost optimization enables competitive pricing and profitability.

**Measurement:** Monthly cloud spending, cost per active rental, cost per booking.

**Source:** Business Context - "improve utilization of the existing fleet before adding more vehicles"

---

## 7. Compliance & Legal Requirements

### NFR-CV-021: Audit Trail & Compliance Logging
**Category:** Compliance  
**Priority:** HIGH

**Requirement:** Maintain immutable audit logs for regulatory compliance and dispute resolution.

**Acceptance Criteria:**
- Audit events logged:
  - All payment transactions (pre-auth, charge, refund)
  - Vehicle access (unlock, lock, remote disable)
  - Fine assessments and adjustments
  - Customer data access/modification (GDPR)
  - Administrative actions (manual fine waiver, dispute resolution)
- Log retention: 7 years (tax, financial regulations)
- Immutability: Write-once storage (append-only logs)
- Tamper detection: Cryptographic hashing to detect log modifications

**Rationale:** Legal requirement for financial transactions, supports dispute resolution, enables fraud detection.

**Measurement:** Audit log completeness checks, compliance audits.

**Source:** FR-CV-019 (GDPR), payment industry regulations

---

### NFR-CV-022: Data Retention & Deletion
**Category:** Compliance  
**Priority:** HIGH

**Requirement:** Comply with data retention policies for legal and privacy requirements.

**Acceptance Criteria:**
- **Active customer data:** Retained while account active + 3 years post-closure
- **Financial records:** 7 years (tax compliance)
- **Telemetry data:** 30 days hot, 1 year warm, 3 years cold archive
- **Photos (return verification):** 90 days after rental completion (dispute window)
- **Audit logs:** 7 years (compliance)
- **Deleted customer data:** Completely purged ≤ 30 days after deletion request (GDPR right to erasure)

**Rationale:** Balance regulatory compliance, business needs, and privacy rights.

**Measurement:** Data retention policy audits, automated deletion job success rates.

**Source:** FR-CV-019 (GDPR Compliance), financial regulations

---

## 8. Integration Requirements

### NFR-CV-023: Third-Party Service Resilience
**Category:** Integration  
**Priority:** HIGH

**Requirement:** Gracefully handle third-party service failures to prevent system-wide outages.

**Acceptance Criteria:**
- Circuit breakers: Trip after 5 consecutive failures, half-open retry after 60 seconds
- Timeout policies:
  - Payment gateway: 10-second timeout
  - AI photo analysis: 30-second timeout
  - Mapping services: 5-second timeout
- Fallback strategies:
  - Payment: Queue for retry, allow unlock with manual review
  - AI: Route to human crew review
  - Maps: Use cached data or simplified routing
- Health checks: Periodic health monitoring (every 30 seconds)

**Rationale:** Third-party dependencies are outside our control. Must prevent cascading failures.

**Measurement:** Circuit breaker trip frequency, fallback activation rates, dependency uptime metrics.

**Source:** Transcript - "Third party services, third party mapping services, routing services"

---

### NFR-CV-024: API Versioning & Backward Compatibility
**Category:** Integration  
**Priority:** MEDIUM

**Requirement:** Support mobile app backward compatibility during rolling updates.

**Acceptance Criteria:**
- API versioning: Semantic versioning (v1, v2, etc.)
- Backward compatibility window: Support N-1 API version for minimum 6 months
- Deprecation notices: 90-day advance notice for breaking changes
- Mobile app support: Support iOS/Android versions from last 2 years

**Rationale:** Customers don't always update mobile apps immediately. Must support graceful migration.

**Measurement:** API version usage analytics, deprecated endpoint traffic.

**Source:** Mobile-first architecture best practices

---

## 9. AI-Specific Non-Functional Requirements

### NFR-AI-001: AI Model Accuracy & Quality
**Category:** AI Quality  
**Priority:** HIGH

**Requirement:** AI models must achieve minimum accuracy thresholds for production use.

**Acceptance Criteria:**
- **Cleanliness Assessment (FR#2L):**
  - True Positive Rate: ≥ 90% (correctly flags dirty vehicles)
  - False Positive Rate: ≤ 5% (incorrectly flags clean vehicles)
  - Human review required when confidence < 70%
- **Damage Detection (FR#2M):**
  - Precision: ≥ 85% (flagged damage is real)
  - Recall: ≥ 80% (catches most actual damage)
  - mAP@0.5 ≥ 0.75 (mean Average Precision for object detection)
  - Human review required when confidence < 80%
- **Demand Forecasting (FR#2H):**
  - Prediction accuracy: ≥ 75% within ±15% margin (e.g., predict 100 bookings, actual 85-115)
  - Forecast horizon: 24-72 hours ahead

**Rationale:** Poor AI accuracy erodes customer trust (false fines) and business value (incorrect forecasts).

**Measurement:** Confusion matrices, A/B testing against human baseline, production accuracy monitoring.

**Related ADRs:**
- ADR-0014: ML Model Selection for Cleanliness Assessment
- ADR-0020: Damage Detection Model Architecture

**Source:** FR#2L, FR#2M, FR#2H, Business Context cleanliness verification

---

### NFR-AI-002: AI Model Performance & Latency
**Category:** AI Performance  
**Priority:** HIGH

**Requirement:** AI inference must complete within acceptable timeframes to support real-time workflows.

**Acceptance Criteria:**
- **Photo Analysis (Cleanliness/Damage):**
  - Single image inference: ≤ 5 seconds (p95)
  - Batch processing (4 photos per return): ≤ 15 seconds total
  - Timeout fallback: Route to human review if exceeds 30 seconds
- **Demand Forecasting:**
  - Forecast generation: ≤ 60 seconds for 24-hour ahead prediction
  - Batch forecasting: ≤ 10 minutes for 7-day rolling forecasts (all vehicles)

**Rationale:** Customer waiting at return expects timely assessment. Slow AI degrades UX to unacceptable levels.

**Measurement:** AI inference latency metrics, timeout rates, fallback activation frequency.

**Source:** FR-CV-007 (Return verification), mobile app usability expectations

---

### NFR-AI-003: AI Model Monitoring & Drift Detection
**Category:** AI Reliability  
**Priority:** HIGH

**Requirement:** Continuously monitor AI model performance to detect degradation or drift.

**Acceptance Criteria:**
- **Production Monitoring:**
  - Track accuracy metrics daily (compare human review labels vs AI predictions)
  - Alert if accuracy drops >10% from baseline over 7-day rolling window
  - Track confidence score distributions (detect shift toward low-confidence predictions)
- **Data Drift Detection:**
  - Monitor input image characteristics (brightness, resolution, aspect ratio)
  - Alert if input distribution shifts significantly (e.g., new camera models)
- **Retraining Triggers:**
  - Automatic retraining pipeline if accuracy <80% for 14 consecutive days
  - Manual retraining on-demand with minimum 1,000 new labeled examples

**Rationale:** AI models degrade over time due to data drift, concept drift, or environmental changes.

**Measurement:** Model performance dashboards, drift detection alerts, retraining frequency.

**Source:** Judges' criteria - "handling AI uncertainty", "validation and verification of AI results"

---

### NFR-AI-004: AI Vendor Lock-In Mitigation
**Category:** AI Portability  
**Priority:** MEDIUM

**Requirement:** Architecture must support switching AI vendors/models without major refactoring.

**Acceptance Criteria:**
- **Abstraction Layer:** AI inference wrapped in service interface (not direct vendor SDK calls)
- **Multi-Vendor Testing:** Validate at least 2 vendors during POC (e.g., Google Vertex AI + AWS Rekognition)
- **Model Format Portability:** Prefer ONNX or TensorFlow Lite for custom models (vendor-agnostic)
- **Feature Parity:** Document vendor-specific features used, maintain fallback implementations
- **Migration Plan:** Document step-by-step process to switch vendors (estimated <2 weeks)

**Rationale:** AI vendor landscape is volatile (pricing changes, service shutdowns, quality degradation).

**Measurement:** Abstraction layer test coverage, vendor migration simulation (annual drill).

**Source:** Judges' criteria - "How would you switch from one AI model to another if the quality degraded?"

---

### NFR-AI-005: AI Cost Management & Optimization
**Category:** AI Cost  
**Priority:** MEDIUM

**Requirement:** AI inference costs must remain economically viable at scale.

**Acceptance Criteria:**
- **Target Cost per Analysis:**
  - Cleanliness assessment: ≤ €0.10 per rental (4 photos)
  - Damage detection: ≤ €0.15 per inspection (6 photos)
- **Cost Optimization Strategies:**
  - Batch inference where possible (reduce per-request overhead)
  - Image compression before upload (reduce data transfer costs)
  - Edge caching for common scenarios (e.g., "clearly clean" fast path)
  - Auto-scale inference compute based on demand (avoid idle costs)
- **Budget Alerts:** Notify ops team if daily AI costs exceed €1,000 (10,000 rentals/day baseline)

**Rationale:** AI inference costs can become prohibitive at scale. Must optimize to maintain profitability.

**Measurement:** Cost per inference metric, monthly AI vendor invoices, cost trend analysis.

**Source:** Judges' criteria - "How would you handle pricing volatility?", business cost optimization goals

---

### NFR-AI-006: AI Explainability & Transparency
**Category:** AI Ethics  
**Priority:** MEDIUM

**Requirement:** AI decisions must be explainable to support customer trust and dispute resolution.

**Acceptance Criteria:**
- **Confidence Scores:** All AI predictions include confidence score (0-100%)
- **Explanation Metadata:**
  - Cleanliness: Bounding boxes around detected stains/debris
  - Damage: Annotated regions of detected damage
- **Human Review Audit Trail:** Log AI prediction + human override decision + justification
- **Customer Communication:**
  - Show confidence score in fine notifications (e.g., "AI detected stain with 92% confidence")
  - Provide annotated photos as evidence in dispute resolution

**Rationale:** Customers must understand why fines were assessed. Explainability builds trust and reduces disputes.

**Measurement:** Dispute win rate (target >90%), customer satisfaction scores on fine transparency.

**Source:** FR-CV-020 (Dispute Resolution), AI ethics best practices

---

### NFR-AI-007: AI Training Data Quality & Bias Prevention
**Category:** AI Quality  
**Priority:** MEDIUM

**Requirement:** AI models must be trained on diverse, representative, high-quality datasets.

**Acceptance Criteria:**
- **Dataset Diversity:**
  - Photos from all vehicle types (cars, vans, different makes/models)
  - Various lighting conditions (day, night, indoor parking)
  - Weather conditions (rain, snow, sun)
  - Geographic diversity (different EU countries)
- **Labeling Quality:**
  - Inter-annotator agreement: ≥ 85% (Kappa score)
  - Double-blind labeling for training data
  - Expert review for edge cases
- **Bias Testing:**
  - Test model performance across vehicle types (no >10% accuracy difference)
  - Test across weather conditions (no >15% accuracy difference)
  - Regular fairness audits (semi-annual)

**Rationale:** Biased models lead to unfair customer treatment and reputational damage.

**Measurement:** Dataset composition reports, bias test results, fairness audit findings.

**Source:** AI ethics guidelines, ML best practices

---

### NFR-AI-008: AI System Fallback & Graceful Degradation
**Category:** AI Resilience  
**Priority:** HIGH

**Requirement:** System must function when AI services are unavailable or unreliable.

**Acceptance Criteria:**
- **Fallback Mechanisms:**
  - AI service down → Route all to human crew review (SLA: 24 hours)
  - AI low confidence (< threshold) → Human review (SLA: 4 hours)
  - AI timeout → Retry once, then human review
- **Degraded Mode Operations:**
  - Accept customer return even if AI unavailable (assess fine later)
  - Show warning in app: "Photo verification delayed due to technical issues"
- **Recovery Protocol:**
  - Batch process queued photos when AI service recovers
  - Prioritize high-value cases (luxury vehicles, corporate accounts)

**Rationale:** AI is not 100% reliable. Must avoid blocking customer workflows when AI fails.

**Measurement:** Fallback activation rate, human review queue depth, SLA compliance.

**Source:** NFR-CV-010 (Fault Tolerance), FR-CV-008 human-in-the-loop design

---

### NFR-AI-009: AI Security & Adversarial Robustness
**Category:** AI Security  
**Priority:** MEDIUM

**Requirement:** Protect AI models from adversarial attacks and data poisoning.

**Acceptance Criteria:**
- **Input Validation:**
  - Reject manipulated images (e.g., adversarial noise, watermarks designed to fool AI)
  - Hash verification: Ensure photos taken in-app, not uploaded from gallery
- **Model Protection:**
  - API rate limiting to prevent model extraction attacks
  - No direct model access for customers (only inference results)
- **Adversarial Testing:**
  - Regular red team exercises to test robustness
  - Test against known adversarial attack techniques (FGSM, PGD)

**Rationale:** Malicious users may attempt to fool AI to avoid fines. Must detect and prevent gaming.

**Measurement:** Adversarial test pass rate, suspicious photo submission detection rate.

**Source:** AI security best practices, fraud prevention requirements

---

### NFR-AI-010: Demand Forecasting Accuracy Requirements
**Category:** AI Quality  
**Priority:** MEDIUM

**Requirement:** AI demand forecasting must provide actionable predictions for fleet positioning.

**Acceptance Criteria:**
- **Prediction Accuracy:**
  - Short-term (next 4 hours): ≥ 80% accuracy within ±20% margin
  - Medium-term (24 hours): ≥ 75% accuracy within ±25% margin
  - Long-term (7 days): ≥ 65% accuracy within ±30% margin
- **Prediction Granularity:**
  - Time: Hourly predictions
  - Location: City zone level (~5km radius)
  - Vehicle type: Separate predictions for cars vs vans
- **Forecast Update Frequency:**
  - Rolling forecasts updated every 6 hours
  - Incorporate real-time events (weather changes, local events)

**Rationale:** Forecasting drives vehicle repositioning decisions. Inaccurate forecasts waste crew time and reduce utilization.

**Measurement:** Forecast vs actual booking volume, Mean Absolute Percentage Error (MAPE), crew repositioning efficiency.

**Related ADRs:**
- ADR-0022: Demand Forecasting Model Selection and Architecture

**Source:** Business Context - "forecast demand... predict, well, we're going to need more E bikes here"

---

## Requirements Traceability Matrix

| NFR ID | Requirement Name | Category | Priority | Related FRs | Business Goal | Source |
|--------|------------------|----------|----------|-------------|---------------|--------|
| NFR-CV-001 | Booking Response Time | Performance | HIGH | FR-CV-001, FR-CV-002 | Customer Satisfaction | E-commerce UX standards |
| NFR-CV-002 | Vehicle Unlock Latency | Performance | HIGH | FR-CV-005 | Customer Satisfaction | Transcript (unlock) |
| NFR-CV-003 | Real-Time Telemetry Processing | Performance | MEDIUM | FR-CV-012 | Operational Efficiency | Transcript (telemetry 30s) |
| NFR-CV-004 | Payment Processing Speed | Performance | MEDIUM | FR-CV-009, FR-CV-021 | Customer Satisfaction | Payment industry standards |
| NFR-CV-005 | Concurrent User Support | Scalability | HIGH | All booking FRs | Revenue Growth | Business goals (70% utilization) |
| NFR-CV-006 | Data Volume Handling | Scalability | MEDIUM | FR-CV-012, FR-CV-019 | Cost Optimization | Data volume projections |
| NFR-CV-007 | Geographic Distribution | Scalability | MEDIUM | FR-CV-023 | EU Market Expansion | Transcript (EU only) |
| NFR-CV-008 | Service Availability SLA | Availability | HIGH | All core services | Revenue Protection | Industry SaaS standards |
| NFR-CV-009 | Data Durability & Backup | Reliability | HIGH | FR-CV-009, FR-CV-019 | Compliance & Trust | GDPR, financial regs |
| NFR-CV-010 | Fault Tolerance & Resilience | Reliability | MEDIUM | FR-CV-013, AI services | Operational Continuity | Cloud architecture best practices |
| NFR-CV-011 | Authentication & Authorization | Security | HIGH | FR-CV-005, FR-CV-016 | Trust & Safety | Authentication standards |
| NFR-CV-012 | Payment Data Security (PCI DSS) | Security | HIGH | FR-CV-009, FR-CV-021 | Compliance | PCI DSS requirements |
| NFR-CV-013 | Data Privacy (GDPR Compliance) | Security | HIGH | FR-CV-019 | Compliance | GDPR requirements |
| NFR-CV-014 | API Security | Security | MEDIUM | All API interactions | Security & Fraud Prevention | API security best practices |
| NFR-CV-015 | Mobile App Usability | Usability | HIGH | FR-CV-001 to FR-CV-003 | Customer Satisfaction | Mobile UX standards |
| NFR-CV-016 | Multi-Language Support | Usability | MEDIUM | FR-CV-023 | Market Reach | EU language diversity |
| NFR-CV-017 | Crew Mobile App Usability | Usability | MEDIUM | FR-CV-027 | Operational Efficiency | Field operations requirements |
| NFR-CV-018 | Monitoring & Observability | Operational | HIGH | All services | Operational Excellence | SRE/DevOps standards |
| NFR-CV-019 | Deployment & Release Management | Operational | MEDIUM | All services | Agility & Quality | CI/CD best practices |
| NFR-CV-020 | Cost Optimization | Operational | MEDIUM | All services | Profitability | Business cost optimization |
| NFR-CV-021 | Audit Trail & Compliance Logging | Compliance | HIGH | FR-CV-009, FR-CV-019, FR-CV-020 | Legal Compliance | Financial & GDPR regs |
| NFR-CV-022 | Data Retention & Deletion | Compliance | HIGH | FR-CV-019 | Legal Compliance | GDPR, tax regulations |
| NFR-CV-023 | Third-Party Service Resilience | Integration | HIGH | FR-CV-013, AI services | Reliability | Distributed systems patterns |
| NFR-CV-024 | API Versioning & Backward Compatibility | Integration | MEDIUM | Mobile app APIs | Customer Continuity | Mobile API versioning |
| **NFR-AI-001** | **AI Model Accuracy & Quality** | **AI Quality** | **HIGH** | **FR-CV-008, FR-CV-025** | **Trust & Cost Optimization** | **Judges' AI criteria** |
| **NFR-AI-002** | **AI Model Performance & Latency** | **AI Performance** | **HIGH** | **FR-CV-007, FR-CV-008** | **Customer Satisfaction** | **Real-time UX expectations** |
| **NFR-AI-003** | **AI Model Monitoring & Drift Detection** | **AI Reliability** | **HIGH** | **All AI features** | **Quality Assurance** | **Judges' AI monitoring** |
| **NFR-AI-004** | **AI Vendor Lock-In Mitigation** | **AI Portability** | **MEDIUM** | **All AI features** | **Risk Mitigation** | **Judges' vendor lock-in** |
| **NFR-AI-005** | **AI Cost Management & Optimization** | **AI Cost** | **MEDIUM** | **All AI features** | **Profitability** | **Judges' pricing volatility** |
| **NFR-AI-006** | **AI Explainability & Transparency** | **AI Ethics** | **MEDIUM** | **FR-CV-008, FR-CV-020** | **Trust & Transparency** | **AI ethics standards** |
| **NFR-AI-007** | **AI Training Data Quality & Bias Prevention** | **AI Quality** | **MEDIUM** | **All AI features** | **Fairness & Accuracy** | **ML best practices** |
| **NFR-AI-008** | **AI System Fallback & Graceful Degradation** | **AI Resilience** | **HIGH** | **FR-CV-008, FR-CV-025** | **Operational Continuity** | **Fault tolerance patterns** |
| **NFR-AI-009** | **AI Security & Adversarial Robustness** | **AI Security** | **MEDIUM** | **FR-CV-008, FR-CV-025** | **Fraud Prevention** | **AI security research** |
| **NFR-AI-010** | **Demand Forecasting Accuracy Requirements** | **AI Quality** | **MEDIUM** | **FR-CV-017** | **Utilization Optimization** | **Business forecasting goals** |

---

## Summary

This document defines **34 non-functional requirements** across 9 categories:
- **Performance:** 4 requirements
- **Scalability:** 3 requirements
- **Availability & Reliability:** 3 requirements
- **Security:** 4 requirements
- **Usability:** 3 requirements
- **Operational:** 3 requirements
- **Compliance:** 2 requirements
- **Integration:** 2 requirements
- **AI-Specific:** 10 requirements

### Key Highlights for Judges:

1. ✅ **Comprehensive AI NFRs:** Dedicated section addressing accuracy, performance, monitoring, vendor lock-in, cost, explainability, bias, resilience, security, and forecasting
2. ✅ **Judge Criteria Alignment:** Directly addresses "AI uncertainty handling", "validation/verification", "pricing volatility", "vendor switching"
3. ✅ **Business Value Connection:** Every NFR traceable to business goals (customer satisfaction, cost optimization, compliance, trust)
4. ✅ **Realistic Targets:** Based on industry standards (99.5% uptime, <5% false positives, €0.10 per AI inference)
5. ✅ **Measurement Focus:** Each NFR includes measurable acceptance criteria and monitoring approach
6. ✅ **Trade-Off Awareness:** Acknowledges AI limitations, fallback mechanisms, graceful degradation