# Spec — Part 5: Architecture Standards (ClaimPilot)

**Functional Requirements**
- Modular monolith (Next.js + NestJS) + worker tier; 835/EOB parsing pipeline; payer-rules RAG; provider adapters (LLM/embeddings).
- BAA-covered Postgres (pgvector) + Redis; encrypted PHI storage; Clerk/Auth.js; Stripe; HIPAA infra (Azure/Cloudflare).

**Non-functional Requirements**
- All PHI services under BAA; LLM via zero-retention/BAA endpoints; no lock-in (swappable models); horizontal worker scaling.

**User Stories**
- As an engineer, I want PHI confined to BAA-covered infra with de-identification before LLM.

**Acceptance Criteria**
- No PHI leaves BAA boundary; LLM calls use BAA/zero-retention; swapping model = adapter change only.

**Technical Constraints**
- RLS multi-tenancy; per-practice PHI scoping + encryption; idempotent ingest; citations stored per appeal.

**Edge Cases**
- LLM endpoint without BAA → blocked; provider outage → degrade; large ERA → streaming parse.

**Success Metrics**
- 0 PHI outside BAA infra; review/dashboard p95 < 1.5s; isolated model swaps.
