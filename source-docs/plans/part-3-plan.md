# Plan — Part 3: Business Model (ClaimPilot)

**Objectives:** Per-provider billing + reseller tier + opt-in performance fee with auditable attribution.

**Architecture Decisions:** Stripe per-provider/location + metered; reseller consolidated billing; performance-fee engine on verified Outcomes + audit; tier gating.

**Dependencies:** Stripe (Part 5), outcomes/audit (Parts 4/7/8), metrics (Part 12).

**Milestones:** M1 tiers + provider billing; M2 limit enforcement + upgrade; M3 reseller consolidated billing; M4 performance-fee calc + statement; M5 recoupment/reversal handling.

**Timeline:** ~2 weeks.

**Risks:** performance-fee disputes; recoupment handling; margin (HIPAA COGS).

**Mitigation:** auditable verified-recovery attribution; reversal flow; COGS monitoring < 10%.

**Resource Estimates:** 1 BE + part-time FE, ~2 weeks.
