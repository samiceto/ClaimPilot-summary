# Spec — Part 12: Success Metrics (ClaimPilot)

**Functional Requirements**
- Instrument north-star (recovered revenue, appeal win rate) per practice.
- Track product KPIs (activation, approval/edit rate, KB coverage, AI quality), business KPIs (MRR/ARR, ACV, NRR, margin, CAC payback), compliance/reliability KPIs (PHI-leak=0, deadline adherence=100%).
- Monthly recovered-revenue report per practice; verified-recovery feed for performance fee.

**Non-functional Requirements**
- Metrics PHI-safe (aggregated, no PHI in analytics); accurate from verified outcomes.

**User Stories**
- As an owner, I want a monthly "revenue recovered" report.
- As the team, we want recovery-rate + KB-coverage dashboards.

**Acceptance Criteria**
- Dashboard shows recovered $, win rate, deadline adherence from first use.
- Recovery verified from Outcome data; no PHI in analytics.

**Technical Constraints**
- PostHog (PHI-free) + warehouse; attribution tied to Appeal/Outcome + audit log.

**Edge Cases**
- Sparse-data practices → confidence-bounded metrics; payer recoupment → adjust recovered figures.

**Success Metrics**
- Recovered $ ↑; MRR $25–65k early cohorts; NRR > 115%; margin > 82%; 0 missed deadlines; 0 PHI leaks.
