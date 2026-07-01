# Tasks — Part 10: Quality Standards (ClaimPilot)

- **T10.1 Unit + integration (synthetic PHI)** — Parsers, CARC/RARC, deadline calc, citation linking, ingest/retrieval. Priority: P0. Estimate: 3d. Dependencies: T4.1, T6.3. DoD: coverage on core; synthetic PHI only.
- **T10.2 e2e + deadline-reliability** — ingest→diagnose→draft→approve→track→outcome; deadline never-miss. Priority: P0. Estimate: 2.5d. Dependencies: T4.6. DoD: e2e + deadline tests pass.
- **T10.3 AI eval gates** — Classification/extraction/citation/hallucination/quality in CI. Priority: P0. Estimate: 2d. Dependencies: T6.7. DoD: CI blocks on regression.
- **T10.4 HIPAA control tests + PHI-log scanner** — Access/encryption/audit tests; detect PHI in logs. Priority: P0. Estimate: 1.5d. Dependencies: T7.3, T7.4. DoD: controls verified; PHI-in-log = fail.
- **T10.5 Dashboards + alerting + DR drill** — Recovery/$ recovered/deadline/win-rate dashboards; DR drill. Priority: P1. Estimate: 1.5d. Dependencies: T5.5. DoD: dashboards live; RPO/RTO validated.
