# Tasks — Part 3: Business Model (ClaimPilot)

- [X] **T3.1 Stripe per-provider tiers** — Solo/Practice/Group billing by provider/location. Priority: P0. Estimate: 2d. Dependencies: T5.1. DoD: subscribe + provider-based billing works.
- **T3.2 Tier limit enforcement** — Providers/volume/API/SSO gates + upgrade. Priority: P0. Estimate: 1.5d. Dependencies: T3.1, T4.1. DoD: over-limit prompts upgrade.
- **T3.3 Reseller consolidated billing** — Volume pricing + one invoice for many practices. Priority: P1. Estimate: 2d. Dependencies: T3.1, T2.3. DoD: reseller billed consolidated.
- **T3.4 Performance-fee engine** — Opt-in fee on verified recoveries + statement. Priority: P1. Estimate: 2d. Dependencies: T4.7, T7.4. DoD: auditable; charged on verified recovery only.
- **T3.5 Recoupment/reversal handling** — Reverse fee on payer recoupment. Priority: P2. Estimate: 1d. Dependencies: T3.4. DoD: fee reversed; audited.
