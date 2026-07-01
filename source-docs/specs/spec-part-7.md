# Spec — Part 7: Security Requirements (ClaimPilot)

**Functional Requirements**
- BAAs with all PHI subprocessors; zero-retention/BAA LLM endpoints; HIPAA policies + training + risk assessment.
- PHI minimization/de-identification before LLM; encryption at rest + transit; field-level encryption of identifiers; PHI excluded from logs/analytics.
- Auth with MFA + RBAC + SSO (Group); minimum-necessary; session timeout.
- Per-practice isolation (RLS + scoped encrypted PHI storage); reseller scoping.
- Immutable HIPAA audit of all PHI access/actions; breach-notification process.

**Non-functional Requirements**
- TLS 1.2+; AES-256; HIPAA Security/Privacy Rule compliance; pen test pre-GA.

**User Stories**
- As a compliance officer, I need full PHI audit + BAAs + encryption.
- As an owner, I trust patient data is protected.

**Acceptance Criteria**
- No PHI in logs/analytics/non-BAA services; all PHI access audited; cross-tenant access impossible (tested).
- LLM calls only via BAA/zero-retention endpoints.

**Technical Constraints**
- Postgres RLS; vault/KMS; upload sanitization; OWASP ASVS; CI scanning.

**Edge Cases**
- Subprocessor without BAA → blocked; suspected breach → IR + notification; revoked access → immediate.

**Success Metrics**
- 0 PHI-leak incidents; 100% PHI access audited; HIPAA controls verified each release.
