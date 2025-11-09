# AI-Empowered Fleet Intelligence Platform – 5-Minute Narrative

**Duration:** ~5 minutes  
**Format:** Presentation + short video + repo references  
**Audience:** Business & technical leadership  
**Goal:** Showcase 3 AI-driven use cases, architecture, cost efficiency, and phased rollout

---

## 0:00–0:40 – Customer Voices – Why We’re Doing This
🎥 *Video montage (40 seconds)*

> “When I rent a scooter, I just want it to work.”  
> “Sometimes the bike isn’t where the app says.”  
> “Refunds take forever – I just want clarity.”  
> “If pricing changes mid-day, I want to understand why.”

**Narration:**
> “Our customers want a reliable, transparent, and fair experience.  
> That’s what our AI-empowered fleet platform delivers – connecting every vehicle, every mode, and every process through intelligent automation.”

---

## 0:40–1:20 – Context & Ambition
**Slide:** *Scale of Multi-Modal Complexity*

> “We operate **2 800 cars and vans**, **8 500 bikes**, and **12 000 scooters** across cities.  
> Historically, each mode ran on its own systems and data silos.  
>
> Our mission is to unify this ecosystem through intelligent services that continuously learn and adapt:  
> - **+12 % utilization**  
> - **−€22 maintenance cost per vehicle per month**  
> - **−45 % dispute refunds**
>
> Let’s look at three **AI-driven use cases** that deliver these results.”

---

## 1:20–2:10 – Use Case 1: Intelligent Forecasting & Smart Repositioning
**References:** [ADR-0007 Fleet Service](../adrs/ADR-0007.md), [ADR-0002 Telemetry Stack](../adrs/ADR-0002.md)

> “Every 15 minutes, our **Forecasting AI** predicts city-level demand with ≤ 18 % error.  
> The **Fleet Service** (ADR-0007) acts as the single source of truth, publishing live vehicle events processed by our autoscaled runtime environment.
>
> When demand spikes, the system automatically generates repositioning tasks – no manual dispatching.  
>
> **Results:**  
> - Idle hours **−18 %**  
> - Peak availability **+12 %**  
> - ~**€240 000 uplift per month**”

---

## 2:10–3:00 – Use Case 2: AI-Based Wear Leveling & Predictive Maintenance
**Reference:** [ADR-0019 Predictive Maintenance](../adrs/ADR-0019.md)

> “The same backbone powers our **Predictive Maintenance AI**.  
> Trained on sensor data, it forecasts wear and proactively schedules servicing.
>
> With **82 % prediction accuracy** and **30 % lower variance in mileage**, we reduce downtime **−22 %** and maintenance cost **€78 → €60 per vehicle**.  
>
> **Value:** ≈ **€160 000 savings per month.**”

---

## 3:00–3:50 – Use Case 3: Vision Intelligence for Damage & Cleanliness Triage
**References:** [ADR-0020 Vision Service](../adrs/ADR-0020.md), [ADR-0016 Human-in-the-Loop](../adrs/ADR-0016.md), [ADR-0008 Semantic Search](../adrs/ADR-0008.md)

> “After each ride, return images are analyzed by our **Vision Intelligence Service**.  
> It detects dirt, scratches, or missing parts with **≥ 93 % precision** and **< 2 % false positives**.
>
> Low-confidence cases route automatically to a **Human-in-the-Loop** reviewer (ADR-0016).  
> All evidence – photos, confidence, reviewer notes – is stored in our **Semantic Search layer** (ADR-0008).  
>
> **Results:**  
> - Manual inspection time **−35 %**  
> - Refund rate **2.8 % → 1.4 %**  
> - Cost per inspection **€1.80 → €0.90**  
> - ≈ **€120 000 savings per month**”

---

## 3:50–4:30 – Intelligent Runtime & Cost Efficiency
**Slide:** *Scalable Intelligent Runtime Costs*

| Intelligent Service | Monthly € | Role |
|----------------------|-----------|------|
| Fleet & Event Core | 2 400 | Data backbone |
| Forecast AI | 900 | Demand prediction |
| Maintenance AI | 1 200 | Predictive repairs |
| Vision AI | 2 800 | Image intelligence |
| Search & Agent Layer | 1 900 | Knowledge + HITL |
| **Total Runtime** | **≈ 9 000 €/month** | **< €0.30 per vehicle** |

> “Our autoscaled container runtime executes services only when needed – no idle capacity.  
> Total operational cost around **€9 000 per month**, delivering **>€500 000 in monthly business value**.”

---

## 4:30–5:00 – 12-Week Phased Delivery
**Slide:** *Phased Rollout Overview*

| Phase | Weeks | Focus | Outcome |
|-------|-------|--------|---------|
| **1** | 1–4 | Forecasting & Repositioning AI | +8 pp utilization / €240 k uplift |
| **2** | 5–8 | Maintenance AI | −22 % downtime / €160 k savings |
| **3** | 9–12 | Vision AI + HITL + Search | −40 % refunds / €120 k savings |

> “Three AI-empowered use cases, one intelligent backbone, twelve weeks to MVP, payback under two months.  
>
> This is how we transform fleet operations into a continuously learning system – delivering reliability for our customers and efficiency for our business.”

---

## Slide Headlines Summary

**Title:**  
**AI-Empowered Fleet Intelligence Platform**

**Subtitle:**  
*Unified, event-driven architecture – €9k monthly runtime, €520k value realized in 12 weeks.*

**Core Storyline:**  
- Customers want reliability and fairness.  
- Unified, intelligent architecture with self-learning services.  
- Three AI-driven use cases: forecasting, maintenance, and vision triage.  
- Delivered in 12 weeks, payback < 2 months.
