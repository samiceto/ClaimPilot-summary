# Constitution — Part 9: Scalability Rules (ClaimPilot)

**Scaling model:** stateless API + horizontally scalable worker tier (BullMQ) for ingest/classify/draft/track; managed BAA Postgres (pgvector) + Redis; encrypted PHI storage. All scaling stays within BAA-covered infra.

**Ingestion at scale:** async 835/EOB parsing; batch ERA files (many claims per file); idempotent by claim/check ref; backpressure on LLM rate limits.

**Retrieval at scale:** payer-rules vector index (shared rules KB + per-tenant context); cache payer-rule embeddings + appeal templates; rules KB largely shared (non-PHI) → high cache reuse.

**Drafting at scale:** batch appeal drafting; queue + rate-limit frontier (BAA endpoint); reuse templates per payer/denial-reason to cut cost.

**Deadline engine:** reliable scheduled jobs for appeal deadlines + follow-ups; redundant alerting; never-miss guarantee with audit.

**Data scaling:** indexes on tenant_id + payer + status + deadline; partition/archival of resolved claims; PHI access paths optimized + logged.

**Performance targets:** denial classification near-real-time on ingest; appeal draft p95 < 12s (or async w/ progress); dashboard p95 < 1.5s; deadline alerts fire reliably.

**Reliability:** retries + backoff + DLQ; idempotent jobs; resume partial batches; no missed deadlines, no duplicate appeals.

**Cost-aware scaling:** managed BAA infra; cheap models + caching; shared rules KB reuse; scale workers by queue depth.

**Consistency check:** delivers NFRs (specs), preserves margin (Part 3), supports reliability (Part 10), within HIPAA infra (Part 7).
