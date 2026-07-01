# Tasks — Part 6: AI Strategy (ClaimPilot)

- [X] **T6.1 Denial classification** — Cheap/open model on CARC/RARC + context. Priority: P0. Estimate: 2.5d. Dependencies: T5.4. DoD: classification accuracy >90% on golden set.
- [X] **T6.2 Document extraction** — Cheap model + X12/PDF parsers → claim/denial fields. Priority: P0. Estimate: 2.5d. Dependencies: T5.4. DoD: extraction accuracy validated.
- [X] **T6.3 Payer-rule retrieval** — Embeddings + pgvector over rules KB. Priority: P0. Estimate: 2d. Dependencies: T6.2. DoD: relevant rules retrieved at precision threshold.
- [X] **T6.4 Grounded appeal drafting** — Frontier (BAA) synthesis w/ citations; needs-info fallback. Priority: P0. Estimate: 3d. Dependencies: T6.3, T1.1. DoD: hallucination ≈0; no basis→needs-info.
- **T6.5 Success-likelihood model** — Prioritize high-value appeals. Priority: P1. Estimate: 1.5d. Dependencies: T6.1. DoD: calibrated priority score.
- [X] **T6.6 De-identification layer** — Minimize/de-identify PHI before LLM. Priority: P0. Estimate: 2d. Dependencies: T5.4, T7.3. DoD: PHI minimized pre-LLM; verified.
- [X] **T6.7 Eval suite + gates** — Classification/extraction/citation/hallucination/quality evals in CI. Priority: P0. Estimate: 2d. Dependencies: T6.4. DoD: CI blocks on regression.
