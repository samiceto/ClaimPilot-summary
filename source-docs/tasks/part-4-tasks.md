# Tasks — Part 4: Product Requirements (ClaimPilot)

- [X] **T4.1 Denial ingestion + parsing** — 835 ERA + PDF EOB + manual; parse CARC/RARC, payer, amount, claim. Priority: P0. Estimate: 5d. Dependencies: T5.4, T8.1. DoD: parse accuracy validated; idempotent by claim/check ref.
- [X] **T4.2 Root-cause diagnosis** — Classify reason, appealability, success likelihood, missing-info. Priority: P0. Estimate: 3d. Dependencies: T6.1, T6.5. DoD: diagnosis surfaced per denial.
- [X] **T4.3 Payer-rules KB + retrieval** — Versioned per-payer requirements/deadlines/policy. Priority: P0. Estimate: 3d. Dependencies: T6.3, T8.2. DoD: rules retrievable + cited.
- [X] **T4.4 Appeal drafting** — Payer-specific letter w/ citations + attachments checklist. Priority: P0. Estimate: 4d. Dependencies: T6.4, T4.3. DoD: appeals cited; needs-info when basis missing.
- [X] **T4.5 Approval + e-sign** — Clinician/biller review + attestation before submission. Priority: P0. Estimate: 2d. Dependencies: T2.2, T1.2. DoD: no submission without approval.
- [X] **T4.6 Appeal tracking + deadline engine** — Status, deadlines, follow-ups; never-miss alerts. Priority: P0. Estimate: 3d. Dependencies: T8.4. DoD: deadlines tracked + alerted; 0 missed in tests.
- **T4.7 Outcome capture + analytics** — Record outcomes; denial patterns, recovery rate, $ recovered. Priority: P1. Estimate: 2.5d. Dependencies: T12.1. DoD: outcomes feed metrics + performance fee.
