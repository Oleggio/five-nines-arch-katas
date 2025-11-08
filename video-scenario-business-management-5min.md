# Executive Video Scenario — 5 Minutes (Business Audience)

> Purpose: 5‑minute multi-modal (cars, vans, bikes, scooters) narrative: how our architecture + disciplined AI portfolio improves utilization, reduces operating & dispute costs, and preserves trust. Keeps detail discoverable in repo, not on screen.

---
## 0. Hook (0:00–0:25) — Why now (Whole Company)
- Scale (placeholders): __ cars/vans, __ bikes, __ scooters; multi-modal complexity → margin leakage & service variance.
- Message: "We turn multi-modal fleet signals into higher utilization, lower per‑vehicle cost, and trusted pricing—safely." 
- KPIs to say (fill): Overall utilization __%→__%; Maintenance cost / vehicle / month €__→€__; Dispute refunds __%→__%; Customer satisfaction (NPS) __→__ (target 96%).
- Diagram cue: Single simplified ribbon: Users → Unified Event Backbone → Shared Fleet State / AI Services → Outcomes (color code All vs Mode-specific).

---
## 1. What we solved (0:25–0:55) — Business framing (Multi-Modal)
- Challenges: Demand volatility (weather + events), uneven wear (cars vs scooters), charging / battery rotation inefficiency, dispute processing overhead (photo evidence cars/vans), price elasticity variation (scooters vs cars), compliance (geo / parking).
- Our design: Event-driven core + unified fleet state (ADR-0007) + targeted AI only where KPI gates justify spend.
- Promise: Faster value across all vehicle categories with phased ROI gates; avoid premature heavy ML investment.
- Repo pointer (voiceover): Business drivers & constraints: `requirements/1_0_Business goals & drivers.md`.

---
## 2. Architecture at a glance (0:55–1:30) — Three takeaways
- Shared backbone: Events & APIs let cars/vans, bikes, scooters reuse forecasting, pricing, maintenance scheduling.
- Unified Fleet Service: Single source of truth (availability, location, battery/charge state) → fewer data silos & consistent KPIs.
- Guardrails: Observability, SLAs, rollback paths keep automation safe.

Speaker note: Label benefits not tech ("Predictable Ops", "Faster Change", "Lower Disputes").
- Diagram cue: Start with high-level System Context (include all modes) then quick focus on Container view (only business-facing domains: Booking, Fleet, Pricing, Forecasting, Maintenance, Charging Advice, Vision). Gray out internals.
- Repo pointer: Layered diagrams: `hld/core-func/car-van-rental/1_Context_Diagram.md`, `2_Container_Diagram.md` (+ similar patterns extend to bikes/scooters in `hld/scenarios/`).

---
## 3. Our AI portfolio & recommendations (1:30–2:40) — Value-gated, explainable, swappable
- Demand Forecasting (all modes) — ADR-0022: Start statistical; upgrade only if MAPE plateaus.
  - Gate: 24h MAPE ≤ __%; weather & event features adjust scooter/bike demand separately.
  - Business win: Higher peak availability → Rev/vehicle-hour +__%.
- Dynamic Pricing (elastic segments): Adjust pricing bands per mode elasticity; protect NPS.
  - Gate: Acceptance rate ≥ __%, Utilization band __–__% maintained.
- Maintenance Optimization & Wear Leveling — ADR-0019: Even usage across scooters/bikes and vans reduces downtime.
  - Gate: Wear variance (std dev mileage) ↓ __%; predictive accuracy ≥ __%.
- Charging / Battery Advice — ADR-0021: Simple ML to reduce mid-ride abandonment for scooters/bikes; EV charging optimization for cars/vans.
  - Gate: Abandonment due to battery/charge issues __%→__%.
- Vision (Damage / Cleanliness) — ADR-0020: Phase limited to cars/vans; planned lightweight scooter spot checks later.
  - Gate: Precision ≥ __%, False positives < __%, Reviewer override rate trending ↓.
- Human-in-the-loop Safeguard — ADR-0016: Reviewer overrides feed retraining; prevents automation risk.
- Cost & Risk Control: Model/vendor abstraction; monitor cost per 1k inferences (€__) & storage footprint (ADR-0017 retention).

Speaker note: Emphasize “gates” + “optionality” = disciplined spend & reversible choices.
- Diagram cue: Zoom container diagram area with AI services; add small lock/gate icons near each KPI.
- Repo pointer: ADRs: `adrs/ADR-0022`, `ADR-0019`, `ADR-0021`, `ADR-0020`, `ADR-0016`, `ADR-0017`.

---
## 4. Measurable outcomes (2:40–3:35) — Three cross-fleet mini-stories
1) Forecast → Smart Reposition (all modes):
  - Outcome: Idle hours −__%; Peak availability +__% → Revenue lift €__/mo.
  - Metric: Utilization +__%; Forecast MAPE ↓ to __%; Battery rotation efficiency +__% (scooters/bikes).
2) Wear Leveling & Maintenance Scheduling (scooters/bikes + vans):
  - Outcome: Downtime hours/vehicle −__%; Maintenance cost/vehicle/month €__→€__.
  - Metric: Mileage std dev −__%; Predictive maintenance hit rate __%; Spare parts inventory turns +__%.
3) Damage & Cleanliness Triage (cars/vans now, scooters later):
  - Outcome: Manual inspection time −__%; Dispute refund rate __%→__%; Turnaround time per return −__%.
  - Metric: Cost per inspection €__→€__; False fee rate __%→__%.
- Diagram cue: 3 cropped sequence/flow snippets (return photos, forecast→task dispatch, maintenance scheduling). Highlight only actors & steps tied to value.
- Repo pointer: Sequences & flows: `hld/core-func/car-van-rental/4_Sequence_Diagram.md` + multi-modal extensions in `hld/scenarios/`.

---
## 5. Phasing & ROI (3:35–4:30) — Front-load cross-fleet wins
- Phase 0: Baseline instrumentation (utilization, MAPE per mode, wear variance, dispute metrics).
- Phase 1: Statistical forecasting + initial dynamic pricing bands + manual damage capture.
- Phase 2: Wear leveling algorithm + charging/battery advice; vision shadow mode; refine pricing elasticity.
- Phase 3: Selective automation (damage/cleanliness), advanced forecasting (ML), maintenance prediction scale-out.

ROI slide (fill): Initiative | One-time € | Ongoing €/mo | Uplift €/mo | Savings €/mo | Payback mo | Modes.

Speaker note: “Earn sooner, spend later” – upgrade only at KPI gates; reuse shared backbone reduces incremental cost for new mode features.
- Diagram cue: Simple timeline bar; color segments by mode impact (All / Cars+Vans / Scooters+Bikes / Future).
- Repo pointer: Requirement mapping: `requirements/2_FRs.md`, `3_NFRs.md` (cost ceilings & performance), roadmap notes in `requirements/Appendix C: Future scope.md`.

---
## 6. Trust, compliance, resilience (4:30–4:50) — Safeguards
- Evidence-rich automation: Photos, confidence scores, reviewer overrides logged (reduces false fees & legal exposure).
- Data & retention: ADR-0017 ensures cost control + GDPR alignment (delete non-value media early, retain anonymized training sets).
- Continuous evaluation: ADR-0011 model gates prevent silent drift (forecast & vision).
- Cross-border compliance: Location telemetry enforces territorial rules; reduces fines / manual checks.
- Resilience: Multi-zone deployment, idempotent events, rollback strategies → lower outage cost.
- Diagram cue: Domain model excerpt highlighting audit fields + retention tags + multi-modal attributes.
- Repo pointer: Governance ADRs: `ADR-0011`, `ADR-0017`; Fleet state truth: `ADR-0007`.

---
## 7. Close & ask (4:50–5:00)
- Headline impacts (fill): Overall utilization +__%; Maintenance cost/vehicle −__%; Dispute cost −__%; Faster new market rollout (__ weeks vs baseline __).
- Ask: Approve Phases 1–2 + KPI instrumentation budget; executive dashboard demo in __ weeks (cross-fleet KPIs).

---
## Quick prep checklist (producer view)
- [ ] Architecture slide (multi-modal color coding: All / Cars+Vans / Scooters+Bikes).
- [ ] AI gates slide (MAPE, precision/false positives, wear variance, cost/1k inferences, abandonment).
- [ ] 3 cross-fleet mini-stories with before/after metrics.
- [ ] ROI table (include mode column) filled with conservative assumptions.
- [ ] Consistent service naming; avoid car-only bias.
- [ ] ADR callouts for each AI area (forecast, pricing, maintenance, charging, vision, human-in-loop, retention).
- [ ] Footer: "Full technical detail & decision logs: /hld /adrs /requirements".

---
## Referencing Deep Technical Detail (Presenter Cheat Lines)
Use one-liners to surface depth without distracting:
- "Multi-modal architecture diagrams (context, container, sequences) live under `hld/core-func/*` and `hld/scenarios/`."
- "Decisions & trade-offs: ADRs (forecasting 0022, maintenance 0019, charging advice 0021, vision 0020, human-in-loop 0016, retention 0017)."
- "Requirements & KPI traceability: `requirements/2_FRs.md`, `3_NFRs.md` link functional & non-functional targets to gates."
- "Evaluation cadence: ADR-0011 ensures models stay within agreed KPI envelopes; overrides feed improvement loop."
- "Cost governance: NFR cost ceilings + retention policy (ADR-0017) prevent silent cost creep." 

Optional footer phrase:
> "Deep technical artifacts: /hld (architecture), /adrs (decisions), /requirements (drivers & KPIs)."
