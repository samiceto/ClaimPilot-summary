# Spec — Part 8: Data Standards (ClaimPilot)

**Functional Requirements**
- Canonical entities (Claim, Denial[CARC/RARC], Appeal[citations+status], PayerRule[versioned], Document[835/EOB], Approval, Outcome, AuditEvent...) with PHI fields tagged.
- Payer ERA/EOB = source for denial facts; PayerRules KB canonical for requirements (versioned, dated).
- Idempotent ingest by claim/check ref; dedupe claims/denials; deadline computed from payer rules.
- Citation integrity (appeals link real PayerRule versions); PHI minimization.

**Non-functional Requirements**
- Parse accuracy validated; deadline integrity (0 missed); HIPAA retention + destruction; right-to-access.

**User Stories**
- As a biller, I trust parsed denial data matches the 835/EOB.
- As compliance, I want PHI minimized + retained/destroyed per BAA.

**Acceptance Criteria**
- Re-ingest causes no duplicate claims/appeals; appeals cite real rule versions; deadlines never missed.
- Contract end → PHI returned/destroyed per BAA.

**Technical Constraints**
- pgvector for rules KB; content/claim-ref idempotency; encrypted BAA backups + PITR; restore tests.

**Edge Cases**
- Multi-claim ERA; conflicting payer rules surfaced; partial denial; record-retention vs deletion conflict (legal hold wins).

**Success Metrics**
- Parse accuracy high; 0 missed deadlines; citation correctness; successful encrypted restores.
