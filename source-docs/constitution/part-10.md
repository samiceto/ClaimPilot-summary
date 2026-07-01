# Constitution — Part 10: Quality Standards (ClaimPilot)

**Engineering quality:** TypeScript strict; lint/format gates; PR review; trunk-based + CI; feature flags; PHI-safe test data (synthetic, never real PHI).

**Testing:** unit (835/EOB parsers, CARC/RARC mapping, deadline computation, citation linking); integration (ingest, retrieval, drafting, tracking); e2e (ingest→diagnose→draft→approve→track→outcome); deadline-engine reliability tests; load tests; HIPAA control tests (access, encryption, audit).

**AI quality (core trust gates):** golden-set evals in CI for (a) denial-classification accuracy, (b) extraction accuracy, (c) citation correctness (appeal supported by cited payer rule), (d) hallucination rate ≈0 for clinical/coverage claims, (e) appeal quality. Ship gated on thresholds.

**Grounding & safety gates:** any appeal with fabricated clinical/coverage claim, or missing required citation, or auto-submitted without approval = P0. Missed appeal deadline = P0 reliability incident.

**HIPAA quality gates:** PHI never in logs/analytics; encryption + audit verified each release; access controls tested.

**Observability:** PostHog (PHI-free product analytics), Sentry (PHI-scrubbed), structured PHI-safe logs; dashboards on recovery rate, $ recovered, deadline adherence, appeal win rate.

**Reliability targets:** 99.9% uptime; RPO ≤ 15 min, RTO ≤ 4h; zero missed deadlines; no PHI leakage.

**Definition of Done:** tested (synthetic PHI) + evaluated (grounding/citation gates) + HIPAA-controls verified + documented + observable + human-approval enforced.

**Consistency check:** enforces grounding (Part 1/6), HIPAA (Part 7), product musts (Part 4).
