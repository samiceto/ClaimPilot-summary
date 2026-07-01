# Plan — Part 7: Security Requirements (ClaimPilot)

**Objectives:** Full HIPAA program: BAAs, PHI minimization, encryption, isolation, audit, IR.

**Architecture Decisions:** BAAs with all subprocessors; de-identification layer; AES-256 + field-level encryption; RLS + per-practice PHI scoping; MFA + RBAC + SSO; immutable HIPAA audit; PHI excluded from logs/analytics.

**Dependencies:** Auth, DB/storage (Part 5), data (Part 8).

**Milestones:** M1 BAAs + policies + risk assessment; M2 RLS + RBAC + MFA + SSO; M3 PHI encryption + de-id + log scrubbing; M4 HIPAA audit log; M5 upload sanitization + scanning + IR runbook + pen test.

**Timeline:** ~3 weeks (parallel; BAA + program work spans MVP).

**Risks:** PHI leak; missing BAA; breach.

**Mitigation:** PHI boundary tests; block non-BAA subprocessors; breach-notification process; pen test pre-GA.

**Resource Estimates:** 1 BE (security) + compliance support, ~3 weeks; SOC 2 + HIPAA attestation ongoing.
