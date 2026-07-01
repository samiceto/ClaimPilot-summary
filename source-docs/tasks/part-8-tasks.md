# Tasks — Part 8: Data Standards (ClaimPilot)

- [X] **T8.1 Canonical schema + PHI tagging** — Entities + PHI/financial/secret classification. Priority: P0. Estimate: 2d. Dependencies: T5.2. DoD: schema migrated; PHI fields tagged.
- [X] **T8.2 Versioned payer KB + citation linking** — PayerRule versioning; appeal→rule links. Priority: P0. Estimate: 2d. Dependencies: T8.1. DoD: versions tracked; citations link real rule versions.
- [X] **T8.3 Idempotent ingest + dedupe** — Claim/check-ref idempotency; dedupe claims/denials. Priority: P0. Estimate: 1.5d. Dependencies: T8.1, T4.1. DoD: re-ingest causes no dups.
- **T8.4 Deadline computation engine** — Deadlines from payer rules; never-miss integrity. Priority: P0. Estimate: 2d. Dependencies: T8.2. DoD: deadlines computed; integrity tests pass.
- **T8.5 Retention/destruction + encrypted backups** — HIPAA retention, BAA destruction, PITR + restore test. Priority: P1. Estimate: 1.5d. Dependencies: T8.1. DoD: retention/destruction work; encrypted restore passes.
