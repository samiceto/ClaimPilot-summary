# Plan — Part 8: Data Standards (ClaimPilot)

**Objectives:** Canonical model w/ PHI tagging, versioned payer KB, idempotent ingest, deadline integrity, citation integrity, retention/destruction.

**Architecture Decisions:** ERA/EOB = denial source, PayerRules KB canonical+versioned; claim/check-ref idempotency; deadline computed from rules; appeals store rule versions; classification + HIPAA retention/destruction jobs.

**Dependencies:** Ingestion (Part 4), HIPAA (Part 7), vector (Part 5).

**Milestones:** M1 schema + PHI tagging; M2 versioned payer KB + citation linking; M3 idempotent ingest + dedupe; M4 deadline computation engine; M5 retention/destruction + encrypted backups/restore.

**Timeline:** ~2.5 weeks (overlaps Part 4).

**Risks:** duplicate claims/appeals; missed deadlines; retention vs destruction conflict.

**Mitigation:** claim-ref idempotency; deadline integrity tests; legal-hold rules; restore tests.

**Resource Estimates:** 1 BE, ~2.5 weeks.
