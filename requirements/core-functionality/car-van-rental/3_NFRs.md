# Car/Van Rental - Non-Functional Requirements

## NFR-CV-001: Booking Consistency
**Category:** Data Integrity  
**Requirement:** Zero double-booking tolerance  
**Target:** 100% reservation uniqueness per vehicle per timeslot  
**Source:** Business-critical requirement (revenue loss risk)  
**Implementation:** ACID transactions in PostgreSQL (ADR-0001)

---

## NFR-CV-002: Telemetry Performance
**Category:** Performance  
**Requirement:** Real-time vehicle tracking during rental  
**Target:** GPS updates every 30 seconds, 99th percentile latency < 5s  
**Source:** Transcript 00:28:45 ("at least every 30 seconds")  
**Implementation:** TimescaleDB for time-series (ADR-0002)

---

## NFR-CV-003: Payment Processing Reliability
**Category:** Reliability  
**Requirement:** Payment failures must not block rental completion  
**Target:** Retry mechanism with 3 attempts, fallback to async reconciliation  
**Source:** Industry standard (failed payments = customer support escalation)  
**Implementation:** See ADR-0005 (Payment Gateway Strategy)

---

[Add 10-15 NFRs covering: scalability, availability, security, compliance, observability...]