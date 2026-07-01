# Constitution — Part 4: Product Requirements (ClaimPilot)

**Core capabilities (MVP):**
1. **Denial ingestion:** import EOBs / 835 ERA files (and manual entry); parse denial codes (CARC/RARC), payer, amount, claim/service details.
2. **Root-cause diagnosis:** classify denial reason; determine appealability + likely success; flag missing info.
3. **Appeal drafting:** payer-specific appeal letter with correct clinical + policy/coverage citations and required attachments checklist.
4. **Payer rules knowledge base:** per-payer appeal requirements, deadlines, addresses/portals, policy references — compounding per specialty/payer.
5. **Appeal tracking:** status, deadlines, follow-ups, outcomes to resolution; never miss a filing deadline.
6. **Human approval:** clinician/biller reviews + approves before submission; e-sign/clinical attestation where required.
7. **Analytics:** denial patterns, recovery rate, $ recovered, prevention insights.

**Post-MVP:** PM/EHR integrations (claim data sync), auto-submission via clearinghouse/portals, secondary/appeal-level escalation automation, patient-balance workflows.

**Functional musts:** every appeal cites real payer rules + clinical basis; deadlines tracked + alerted; nothing submitted without human approval; PHI minimized.

**Non-functional musts:** denial-parse accuracy; citation correctness; deadline reliability (no missed appeals); HIPAA compliance.

**Out of scope (v1):** hospital inpatient claims; full PM replacement; non-US payers.

**Consistency check:** implements mission (Part 1); bounded by HIPAA (Part 7), data (Part 8), grounding (Part 6), quality (Part 10).
