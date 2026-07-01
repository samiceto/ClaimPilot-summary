# Spec — Part 4: Product Requirements (ClaimPilot)

**Functional Requirements**
- Denial ingestion: 835 ERA + PDF EOB + manual; parse CARC/RARC, payer, amount, claim/service.
- Root-cause diagnosis: classify reason, appealability, success likelihood, missing-info flags.
- Appeal drafting: payer-specific letter w/ clinical + policy citations + attachments checklist.
- Payer rules KB: requirements, deadlines, addresses/portals, policy refs (versioned).
- Appeal tracking: status, deadlines, follow-ups, outcomes; never-miss-deadline alerts.
- Human approval: clinician/biller review + e-sign before submission.
- Analytics: denial patterns, recovery rate, $ recovered, prevention insights.

**Non-functional Requirements**
- Parse accuracy; citation correctness; deadline reliability (0 missed); HIPAA compliance; PHI minimization.

**User Stories**
- As a biller, I import an 835 and get diagnosed denials + drafted appeals.
- As a clinician, I approve before anything is submitted.

**Acceptance Criteria**
- 835/EOB import parses denials; appeals cite real payer rules; deadlines tracked + alerted; nothing submitted without approval.

**Technical Constraints**
- BullMQ async ingest/draft/track; payer-rules RAG; idempotent re-ingest by claim/check ref.

**Edge Cases**
- Multi-claim ERA; partial denials; unknown CARC; missing clinical info; payer-portal-only submission; deadline near-expiry.

**Success Metrics**
- Recovery rate; $ recovered; deadline adherence 100%; time-to-first-appeal.
