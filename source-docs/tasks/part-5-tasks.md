# Tasks — Part 5: Architecture Standards (ClaimPilot)

- [X] **T5.1 Repo + CI/CD + BAA envs** — Monorepo (Next.js+NestJS); pipelines; BAA-covered environments. Priority: P0. Estimate: 2d. Dependencies: none. DoD: deploy to BAA staging via CI.
- [X] **T5.2 Schema + pgvector + RLS + PHI encryption** — BAA Postgres, vector, RLS, field-level PHI encryption. Priority: P0. Estimate: 2.5d. Dependencies: T5.1. DoD: RLS tests pass; PHI encrypted.
- [X] **T5.3 Worker tier + queue** — BullMQ + Redis for ingest/draft/track. Priority: P0. Estimate: 1.5d. Dependencies: T5.1. DoD: jobs processed with retries.
- [X] **T5.4 Parsing pipeline + RAG + adapters** — X12/PDF parsers; payer-rules RAG; BAA LLM/embedding adapters. Priority: P0. Estimate: 2.5d. Dependencies: T5.2. DoD: ingest→retrieve→cite path works; only BAA endpoints.
- **T5.5 PHI-safe observability** — Sentry (scrubbed) + PostHog (PHI-free) + safe logs. Priority: P1. Estimate: 1d. Dependencies: T5.1. DoD: no PHI in telemetry.
