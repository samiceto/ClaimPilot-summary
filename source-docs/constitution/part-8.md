# Constitution — Part 8: Data Standards (ClaimPilot)

**Core entities:** Tenant(Practice), Provider, User, Payer, PayerRule(versioned), Claim, Denial(CARC/RARC), Appeal(with citations + status), AppealEvent, Document(835/EOB/attachment), Approval, Outcome, AuditEvent. PHI fields explicitly tagged.

**Data classification:** **PHI** (patient identifiers, clinical/claim detail), financial (amounts), credentials (payer/integration tokens — secret), non-PHI metadata. PHI handling governed by HIPAA minimum-necessary.

**Source of truth:** payer ERA/EOB is source for denial facts; PayerRules KB is canonical for appeal requirements (versioned, dated). Appeals reference rules via citations.

**Quality:** parse accuracy validated vs raw 835/EOB; dedupe claims/denials by claim+service ref; deadline integrity (computed from payer rules, never missed); citation integrity (appeals link real PayerRule versions).

**PHI minimization:** de-identify before LLM where feasible; store only minimum-necessary PHI; segregate identifiers.

**Retention:** medical/billing records retained per legal requirement (often 6–10y by state); PHI deletion on contract end per BAA (return/destroy); right-to-access supported.

**Privacy:** no selling/secondary use of PHI; no training shared models on PHI; per-tenant export + destruction.

**Lineage & idempotency:** ingest idempotent by claim/check ref; every appeal records retrieved PayerRule versions + model + prompt version.

**Backups:** encrypted, BAA-covered, PITR; periodic restore tests; backups also PHI-protected.

**Consistency check:** supports grounding/citations (Part 6), HIPAA security (Part 7), metrics (Part 12).
