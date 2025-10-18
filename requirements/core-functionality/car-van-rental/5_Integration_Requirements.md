# Car/Van Rental - Integration Requirements

## Upstream Dependencies (We Consume)

### Customer Management Service
**Purpose:** Identity verification and customer profile  
**Integration Type:** REST API (synchronous)  
**SLA Requirement:** < 500ms response time  
**Data Exchanged:**  
- Request: `customerId`  
- Response: `{ kycStatus, drivingLicenseValid, creditScore }`

**Failure Handling:** Cache customer status for 24h, reject booking if service unavailable

---

### Payment Gateway (Stripe)
**Purpose:** Pre-authorization and payment capture  
**Integration Type:** REST API + Webhooks  
**SLA Requirement:** 99.9% availability  
**Related ADR:** ADR-0005 (Payment Gateway Strategy)

**Operations:**
- `POST /preauth` → Create payment hold
- `POST /capture` → Finalize payment after return
- `POST /refund` → Handle cancellations
- Webhook: `payment.failed` → Trigger retry/support ticket

---

### Fleet Management Service
**Purpose:** Vehicle availability and status  
**Integration Type:** Event-driven (Pub/Sub) + REST API  
**Events Consumed:**  
- `VehicleStatusChanged` → Update availability cache  
- `MaintenanceScheduled` → Block reservation timeslots

---

## Downstream Consumers (Who Uses Our APIs)

### AI Demand Forecasting Service
**Data Provided:**  
- Historical booking patterns (batch: nightly BigQuery export)  
- Real-time reservation events (streaming: Pub/Sub)

**API Endpoint:** `GET /api/bookings/analytics`  
**Schema:** See `hld/core-functionality/car-van-rental/6_Data_Models.md#AnalyticsExport`

---

### Mobile App
**APIs Exposed:**  
- `POST /api/reservations` → Create booking  
- `GET /api/reservations/{id}` → Fetch details  
- `POST /api/rentals/{id}/unlock` → Remote unlock  
- `POST /api/rentals/{id}/return` → Submit return proof

---

### Admin Dashboard
**Purpose:** Operations team monitoring  
**Integration Type:** GraphQL API  
**Capabilities:**  
- View all active rentals  
- Manually override return verification  
- Adjust fines  
- Generate revenue reports