# Plan — Part 12: Success Metrics (ClaimPilot)

**Objectives:** Instrument north-star (recovered revenue, win rate) + KPIs (PHI-safe); monthly recovered-revenue report; verified-recovery feed.

**Architecture Decisions:** PostHog (PHI-free) + warehouse; attribution tied to Appeal/Outcome + audit log; recovery verified from Outcome data; performance-fee feed.

**Dependencies:** Outcomes (Part 4), billing (Part 3), AI (Part 6).

**Milestones:** M1 PHI-safe event taxonomy + instrumentation; M2 north-star dashboard; M3 recovery-rate/KB-coverage dashboards; M4 business/cohort dashboards; M5 monthly tenant report + verified-recovery feed.

**Timeline:** ~2 weeks (overlaps).

**Risks:** PHI in analytics; attribution error; payer recoupment skew.

**Mitigation:** PHI-free analytics; outcome-linked attribution; recoupment adjustments.

**Resource Estimates:** 1 BE/analyst, ~2 weeks.
