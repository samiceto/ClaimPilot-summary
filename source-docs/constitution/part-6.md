# Constitution — Part 6: AI Strategy (ClaimPilot)

**Where AI is used:**
1. **Denial classification** (cheap/open model on CARC/RARC + context) — categorize reason, appealability, priority. Batched.
2. **Document extraction** (cheap model + X12/PDF parsers) — pull claim/denial fields from 835/EOB.
3. **Payer-rule retrieval** (embeddings + pgvector) — fetch relevant appeal requirements/policy.
4. **Appeal drafting & reasoning** (frontier model) — compose payer-specific appeal grounded in retrieved rules + clinical context, with citations.
5. **Success-likelihood estimate** (small model) — prioritize high-value appeals.

**Grounding & safety discipline:**
- Appeals MUST cite real payer rules + clinical/policy basis; no fabricated medical facts or coverage claims. If basis missing → request info, don't invent.
- PHI minimized/de-identified before sending to LLM where feasible; only BAA/zero-retention LLM endpoints.
- Human approval mandatory before submission; agent never auto-submits unreviewed clinical content.

**Cost discipline:** cheap/open models for classification/extraction; frontier only for drafting/reasoning; cache payer-rule embeddings + appeal templates; batch ingestion. AI COGS < 10% revenue.

**Model governance:** versioned prompts; eval suite on denial-classification accuracy, extraction accuracy, citation correctness, hallucination rate (≈0 for clinical/coverage claims), appeal-quality; no PHI trains shared models.

**Consistency check:** upholds HIPAA (Part 7), grounding (Part 1), knowledge moat (Part 11), quality (Part 10), margin (Part 3).
