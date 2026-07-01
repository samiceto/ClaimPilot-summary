# Tasks — Part 1: Vision & Mission (ClaimPilot)

- **T1.1 Invariants spec** — HIPAA-first + grounded-appeals + human-approval contract. Priority: P0. Estimate: 1d. Dependencies: none. DoD: invariants referenced by pipeline.
- [X] **T1.2 Approval + grounding gate** — Block submission without approval; block uncited appeal. Priority: P0. Estimate: 1.5d. Dependencies: T6.4, T4.5. DoD: gate enforced in appeal flow.
- [X] **T1.3 North-star dashboard widget** — Recovered $ + win rate. Priority: P0. Estimate: 1.5d. Dependencies: T12.2. DoD: renders from verified outcomes.
- **T1.4 CI invariants gate** — Block builds failing grounding/HIPAA evals. Priority: P0. Estimate: 0.5d. Dependencies: T10.3. DoD: failing eval fails CI.
