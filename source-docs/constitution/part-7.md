# Constitution — Part 7: Security Requirements (ClaimPilot)

**Principles:** HIPAA Security + Privacy Rule compliance; least privilege; PHI minimization; tenant isolation; full auditability. Compliance is both a requirement and a moat.

**HIPAA program:** BAAs with every PHI-handling subprocessor (hosting, DB, storage, LLM, email); zero-retention/BAA LLM endpoints; documented policies, risk assessment, workforce training, sanction policy.

**PHI handling:** minimize + de-identify before LLM where feasible; encrypt PHI at rest (AES-256) + in transit (TLS 1.2+); field-level encryption of identifiers; PHI never in logs/analytics (PostHog gets no PHI; Sentry scrubbed).

**Identity & access:** Clerk/Auth.js with MFA; RBAC (Owner, Manager, Biller, Clinician/Approver, Viewer); SSO for Group; minimum-necessary access; auto session timeout.

**Tenant isolation:** Postgres RLS on all tenant tables; per-practice PHI storage scoping + encryption; no cross-tenant access; reseller console scoped per managed practice.

**Audit (HIPAA §164.312(b)):** immutable audit of all PHI access, ingest, appeal edits, approvals, submissions, exports — who/what/when; tamper-evident; retained per policy.

**App security:** OWASP ASVS; input validation; file-upload sanitization (835/PDF); dependency + secret scanning in CI; pen test pre-GA.

**Incident response:** HIPAA breach-notification process; IR runbook; Sentry alerting; backups + tested restore; BAA breach obligations.

**Consistency check:** protects data (Part 8), enables trust/reseller sales (Part 11), upholds quality (Part 10).
