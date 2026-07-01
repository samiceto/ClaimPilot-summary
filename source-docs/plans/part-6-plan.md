# Plan — Part 6: AI Strategy (ClaimPilot)

**Objectives:** Build cost-routed, PHI-minimized AI: classification, extraction, retrieval, grounded drafting — hallucination ≈0.

**Architecture Decisions:** Cheap models for classification/extraction; embeddings for payer-rules retrieval; frontier (BAA) for drafting/reasoning; de-identification before LLM; grounding mandatory; eval harness in CI; caching + batching.

**Dependencies:** Data/KB (Part 8), pipeline (Part 4), HIPAA (Part 7), grounding (Part 1).

**Milestones:** M1 denial classification; M2 835/EOB extraction; M3 payer-rule retrieval; M4 grounded appeal drafting w/ citations; M5 success-likelihood; M6 eval suite + gates; M7 de-identification layer.

**Timeline:** ~3 weeks (overlaps Part 4).

**Risks:** hallucinated clinical/coverage claims; PHI exposure to LLM; cost.

**Mitigation:** grounding gate; de-id before LLM + BAA endpoints; cheap routing + caching; golden-set evals.

**Resource Estimates:** 1 ML/BE + support, ~3 weeks.
