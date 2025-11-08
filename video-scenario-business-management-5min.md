# Executive Video Scenario — 5 Minutes (Business Audience)

> Purpose: A crisp, 5-minute story that shows how our architecture and AI recommendations drive utilization, cost control, and trust—without unnecessary technical depth.

---
## 0. Hook (0:00–0:25) — Why now
- Message: "We turn fleet data into higher utilization and lower operating cost—safely." 
- Show one slide: problem → approach → outcomes. Keep AI as a means, not the headline.
- KPIs to say (fill): Utilization __%→__%, Dispute refunds __%→__%, Cost / vehicle / month €__→€__.
- Diagram cue: Keep visuals minimal here: a single simplified architecture ribbon (Logo → Data Ingest → Event Core → AI Assist → Outcomes). Avoid detailed boxes yet.

---
## 1. What we solved (0:25–0:55) — Business framing
- Demand swings and asset downtime hurt margin. Disputes erode trust and waste time.
- Our design: event-driven core + focused AI where ROI is proven.
- Promise: Faster value with phased rollout; control costs with clear gates.
- Repo pointer (voiceover): "Full list of business drivers & constraints is in `requirements/1_0_Business goals & drivers.md`."

---
## 2. The architecture at a glance (0:55–1:30) — One slide, three takeaways
- Modular services around an event backbone: booking, fleet state, pricing, forecasting, vision pipeline.
- Why it matters: decoupled teams, safer changes, quicker features.
- Guardrails built-in: observability, SLAs, rollback paths.

Speaker note: Keep tech labels small; annotate each box with a business benefit (e.g., "Faster changes", "Lower disputes").
- Diagram cue: Show System Context (`hld/core-func/car-van-rental/diagrams/system_context.png`). Optional quick dissolve into Container (`hld/core-func/car-van-rental/diagrams/car_van_container_graph.png`) highlighting only 5–6 business-facing domains (gray out internals).
- Repo pointer (subtitle or lower-third): "Layered C4 diagrams in `hld/core-func/car-van-rental/` for engineering deep dive."

---
## 3. Our AI recommendations (1:30–2:40) — Value-gated, explainable, swappable
- Damage detection (vision):
  - Start with managed/AutoML baseline; human-in-the-loop reviews.
  - Promote automation only when precision ≥ __% and false positives < __%.
  - Why business cares: fewer disputes, faster turnarounds, predictable cost per photo.
- Demand forecasting:
  - Begin with statistical model (Prophet-like) for quick wins; upgrade to ML only if MAPE plateaus.
  - KPI gate: 24h MAPE ≤ __% unlocks broader automation.
  - Why business cares: better availability → higher revenue per vehicle-hour.
- Cost and risk control:
  - Abstraction to switch models/vendors; monitor cost per 1k inferences (€__).
  - Retention policy: keep what proves value, delete what creates risk.

Speaker note: Repeat “gates” and “optionality”—execs want control and flexibility.
- Diagram cue: Lightly zoom into container diagram region showing AI services (forecasting, vision, pricing) with gate icons (🔒) next to KPIs.
- Repo pointer: "Decision rationale in ADRs: `adrs/ADR-0020`, `ADR-0022`, `ADR-0016`, `ADR-0017`."

---
## 4. Measurable outcomes (2:40–3:35) — Three mini-stories
1) Return & photos → AI triage + reviewer:
   - Outcome: reduce unjust fees and manual inspection time by __%.
   - Metric: Dispute refund rate __%→__%; cost per inspection €__→€__.
2) Forecasting → repositioning tasks:
   - Outcome: cut idle hours by __%; improve morning peak availability.
   - Metric: Utilization +__%; Rev/vehicle-hour +__%.
3) Dynamic pricing band:
   - Outcome: sustain target utilization (__–__%) without hurting NPS.
   - Metric: Revenue uplift €__/mo; NPS Δ__.
- Diagram cue: Use 3 cropped sequence snippets from `hld/core-func/car-van-rental/4_Sequence_Diagram.md`—each animation under 6s. Highlight only actors & steps tied to value.
- Repo pointer: "Full operational sequences in `4_Sequence_Diagram.md`."

---
## 5. Phasing & ROI (3:35–4:30) — Invest where results show up early
- Phase 0: Instrumentation baseline (no risk, high learning).
- Phase 1: MVP (forecast + manual damage capture) → early utilization lift.
- Phase 2: Shadow AI with human review → prove accuracy, tune costs.
- Phase 3: Selective automation + cost tuning → scale wins.

ROI framing slide (fill): Initiative | One-time € | Ongoing €/mo | Uplift €/mo | Savings €/mo | Payback mo.

Speaker note: Emphasize "earn sooner, spend later" and fast payback items first.
- Diagram cue: Simple phased timeline bar (no detailed Gantt). Optional overlay small KPI gate icons.
- Repo pointer: "Detailed requirement-to-phase mapping in `requirements/2_FRs.md` & `3_NFRs.md`."

---
## 6. Trust, compliance, and resilience (4:30–4:50) — Risk handled
- Evidence-rich decisions (photos, confidence, reviewer trail) → audit-ready.
- GDPR-conscious retention → less future rework cost.
- Resilience: multi-zone services, retries, and rollbacks → fewer costly outages.
- Diagram cue: Domain/Data model excerpt (`hld/core-func/car-van-rental/3_Domain_Model.md`) with privacy & audit attributes highlighted.
- Repo pointer: "Governance & evaluation ADRs: `ADR-0011` (evaluation), `ADR-0017` (retention)."

---
## 7. Close & ask (4:50–5:00)
- 3 headline impacts (fill): Utilization +__%, Dispute cost −__%, Faster rollout to new markets (__ weeks).
- Ask: Approve Phases 1–2 and KPI instrumentation. Demo dashboard in __ weeks.

---
## Quick prep checklist (producer view)
- [ ] One architecture slide with business annotations.
- [ ] AI gates slide (precision/recall, MAPE, cost/1k inferences).
- [ ] 3 mini-stories with before/after metrics.
- [ ] ROI table filled with conservative assumptions.
- [ ] Confirm diagram naming consistency and ADR callouts.
- [ ] Add lower-third or slide footer: "Full technical detail & decision logs: see `/hld/core-func/car-van-rental` & `/adrs`."

---
## Referencing Deep Technical Detail (Presenter Cheat Lines)
Use one-liners so executives know depth exists without forcing them into it:
- "For engineering due diligence, the layered C4 diagrams are in `hld/core-func/car-van-rental/`—context, container, sequences, domain model." 
- "Every major choice has an ADR (decision record) under `adrs/`—title states the decision, each includes trade-offs & revisit triggers." 
- "Requirements traceability lives in `requirements/2_FRs.md` and `3_NFRs.md`—each mapped to KPIs we report." 
- "Model evaluation cadence and gates are documented in `adrs/ADR-0011` so automation never becomes a black box." 
- "If you want to inspect cost governance, look at NFR cost ceilings in `requirements/3_NFRs.md` plus retention policy `ADR-0017`." 

Optional footer phrase for multiple slides:
> "Deeper technical artifacts: /hld (architecture), /adrs (decisions), /requirements (drivers)."
