# Tasks — Part 9: Scalability Rules (ClaimPilot)

- [X] **T9.1 Async batch ERA ingest** — Batch many claims/file; idempotent. Priority: P0. Estimate: 2d. Dependencies: T4.1, T5.3. DoD: large ERA processes w/ progress.
- **T9.2 Worker autoscaling + DLQ** — Scale by queue depth (BAA infra); DLQ. Priority: P1. Estimate: 1.5d. Dependencies: T5.3. DoD: scales under load; failures to DLQ.
- **T9.3 Rules-KB cache + retrieval** — Cache shared rule embeddings/templates. Priority: P0. Estimate: 1.5d. Dependencies: T6.3. DoD: cache hits cut cost; precision holds.
- **T9.4 Batched drafting + resume** — Per-batch draft; resume partial; frontier rate-limit. Priority: P0. Estimate: 1.5d. Dependencies: T4.4. DoD: resumable; targets met.
- **T9.5 Deadline-engine redundancy + load test** — Redundant alerting; 10× load; archival. Priority: P0. Estimate: 2d. Dependencies: T8.4. DoD: 0 missed deadlines at scale.
