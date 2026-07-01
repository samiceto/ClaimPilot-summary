# Spec — Part 9: Scalability Rules (ClaimPilot)

**Functional Requirements**
- Async worker tier (BullMQ) for ingest/classify/draft/track; scale by queue depth; all within BAA infra.
- Batch ERA parsing (many claims/file); idempotent by claim/check ref.
- Payer-rules vector index (shared non-PHI KB → high cache reuse); cache embeddings + templates.
- Batch appeal drafting; queue + rate-limit frontier (BAA); deadline engine with redundant alerting.

**Non-functional Requirements**
- Classification near-real-time on ingest; appeal draft p95 < 12s (or async w/ progress); dashboard p95 < 1.5s; 0 missed deadlines; no duplicate appeals.

**User Stories**
- As a busy practice, I want large ERA files processed reliably and deadlines never missed.

**Acceptance Criteria**
- Big ERA batch processes w/ progress + resume; deadline alerts fire reliably; precision holds as KB grows.

**Technical Constraints**
- Retries + backoff + DLQ; idempotent jobs; indexes on tenant/payer/status/deadline; archival of resolved claims.

**Edge Cases**
- Huge ERA; concurrent practices; LLM rate limit → backpressure; clock/timezone correctness for deadlines.

**Success Metrics**
- Targets hold at scale; 0 missed deadlines; 0 duplicate appeals.
