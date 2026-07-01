# Plan — Part 5: Architecture Standards (ClaimPilot)

**Objectives:** Stand up HIPAA/BAA-covered monolith + worker tier + parsing + RAG + adapters, no lock-in.

**Architecture Decisions:** Next.js + NestJS; BullMQ workers; BAA Postgres (pgvector) + Redis; encrypted PHI storage; BAA/zero-retention LLM adapters; RLS multi-tenancy; Azure/Cloudflare HIPAA infra.

**Dependencies:** BAAs (hosting/DB/storage/LLM/email), Clerk/Auth.js, Stripe.

**Milestones:** M1 repo + CI/CD + BAA envs; M2 schema + pgvector + RLS + PHI encryption; M3 worker tier + queue; M4 parsing pipeline + RAG + adapters; M5 PHI-safe observability.

**Timeline:** ~2.5 weeks (front-loaded; BAA setup adds time).

**Risks:** BAA procurement lead time; PHI boundary leaks; lock-in.

**Mitigation:** start BAAs day 1; de-identification layer; adapters; managed infra.

**Resource Estimates:** 1–2 BE, ~2.5 weeks.
