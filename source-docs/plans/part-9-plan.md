# Plan — Part 9: Scalability Rules (ClaimPilot)

**Objectives:** Scale ingest/classify/draft/track workers + deadline engine; meet latency; never miss deadlines; within BAA infra.

**Architecture Decisions:** Queue-depth autoscaling (BAA infra); batch ERA parsing; shared payer-rules KB caching; batched drafting + frontier rate-limit; redundant deadline scheduler; DLQ.

**Dependencies:** Architecture/AI (Parts 5/6), data (Part 8).

**Milestones:** M1 async ingest (batch ERA); M2 worker autoscaling + DLQ; M3 rules-KB cache + retrieval; M4 batched drafting + resume; M5 deadline engine redundancy + load tests + archival.

**Timeline:** ~2.5 weeks (overlaps + hardening).

**Risks:** large ERA timeouts; deadline-engine failure; rate limits.

**Mitigation:** batched async + resume; redundant alerting + monitoring; backpressure; load test.

**Resource Estimates:** 1 BE, ~2.5 weeks.
