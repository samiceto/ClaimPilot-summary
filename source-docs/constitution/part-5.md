# Constitution — Part 5: Architecture Standards (ClaimPilot)

**Principles:** modular monolith first (Next.js + NestJS) + worker tier; HIPAA-grade, BAA-covered managed infra only; RAG pipeline for payer rules + appeal drafting; provider adapters; no lock-in.

**Reference stack:** Next.js + React + TS + Tailwind + shadcn/ui; NestJS API; **HIPAA/BAA-covered** Postgres (Neon with BAA or Azure Postgres) + Redis; Clerk/Auth.js (BAA-aware) auth; Stripe billing; BullMQ for ingest/draft/tracking jobs; encrypted PHI object storage (R2/Azure with BAA); Azure (HIPAA infra) + Cloudflare edge; PostHog (no PHI) + Sentry (PHI-scrubbed); Anthropic + OpenAI **under BAA/zero-retention** behind adapter; cheap/open models for denial classification.

**Standards:**
- All PHI-handling services under BAA; PHI minimized + de-identified before LLM where possible; LLM calls use zero-retention/BAA endpoints.
- 835/EOB parsing pipeline (X12 835 + PDF EOB); idempotent re-ingest by claim/check ref.
- Payer-rules RAG (pgvector) with citations stored per appeal.
- Multi-tenant Postgres RLS; per-practice isolation; PHI storage scoped + encrypted per tenant.
- 12-factor; secrets in vault/KMS; least-privilege; full audit (HIPAA).

**Consistency check:** enforces HIPAA (Part 7), grounding (Part 6), scalability (Part 9), cost (Part 3).
