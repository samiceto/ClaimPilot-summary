# Spec — Part 6: AI Strategy (ClaimPilot)

**Functional Requirements**
- Denial classification (cheap/open model) → reason, appealability, priority.
- Document extraction (cheap model + X12/PDF parsers) → claim/denial fields.
- Payer-rule retrieval (embeddings + pgvector).
- Appeal drafting/reasoning (frontier, BAA endpoint) grounded in retrieved rules + clinical context, with citations.
- Success-likelihood estimate (small model) to prioritize.

**Non-functional Requirements**
- Hallucination ≈0 on clinical/coverage claims; PHI minimized/de-identified before LLM; AI COGS < 10%; versioned prompts.

**User Stories**
- As a biller, I want accurate denial diagnosis + cited appeal drafts.
- As compliance, I want PHI minimized before any model call.

**Acceptance Criteria**
- Grounding gate: appeal cites real payer rule or requests info (no fabrication).
- Eval gates: classification > 90%, extraction high, citation correct, hallucination ≈0.
- Frontier only for drafting/reasoning; classification/extraction on cheap models; only BAA LLM endpoints.

**Technical Constraints**
- Difficulty routing; de-identification step; cache payer-rule embeddings + templates; batch ingest; queue/rate-limit frontier.

**Edge Cases**
- Unknown payer/CARC → needs-info; missing clinical basis → request from clinician; conflicting rules → surface + flag.

**Success Metrics**
- Approval/edit rate high; hallucination ≈0; AI COGS < 10%.
