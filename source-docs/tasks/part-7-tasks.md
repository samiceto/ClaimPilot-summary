# Tasks — Part 7: Security Requirements (ClaimPilot)

- **T7.1 BAAs + HIPAA program** — Execute BAAs (hosting/DB/storage/LLM/email); policies, risk assessment, training. Priority: P0. Estimate: 3d (+ procurement lead). Dependencies: none. DoD: BAAs signed; non-BAA subprocessors blocked.
- [X] **T7.2 RLS + RBAC + MFA + SSO** — Tenant RLS, roles, MFA, SSO (Group), reseller scoping. Priority: P0. Estimate: 2.5d. Dependencies: T5.2. DoD: RLS tests pass; MFA/SSO work.
- [X] **T7.3 PHI encryption + de-id + log scrubbing** — Field-level encryption; de-id; PHI out of logs/analytics. Priority: P0. Estimate: 2d. Dependencies: T5.2. DoD: PHI encrypted; no PHI in telemetry (scanner-verified).
- [X] **T7.4 HIPAA audit log** — Immutable audit of all PHI access/actions. Priority: P0. Estimate: 1.5d. Dependencies: T5.2. DoD: PHI access fully audited + tamper-evident.
- **T7.5 Sanitization + scanning + IR + pen test** — Upload scan, dep/secret scan, IR runbook, breach process, pen test. Priority: P1. Estimate: 2d. Dependencies: T5.1. DoD: scans on; runbook + pen test pre-GA.
