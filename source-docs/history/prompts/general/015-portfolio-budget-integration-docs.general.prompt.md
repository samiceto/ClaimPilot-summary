---
id: 015
title: Create portfolio, budget, and integration docs
stage: general
date: 2026-06-27
surface: agent
model: claude-opus-4-8
feature: none
branch: none
user: samiullahmalik7474@gmail.com
command: general
labels: ["docs", "portfolio", "budget", "integration", "sales"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
 - portfolio_description.md (new)
 - budget.md (new)
 - how_to_integrate.md (new)
tests:
 - none (docs; grounded in actual stack via package.json + schema inspection)
---

## Prompt

create 3 files: 1. portfolio_description.md (client-facing product details for a portfolio page),
2. budget.md (cost analysis of running the project per the stack involved),
3. how_to_integrate.md (step-by-step to make the project work for a client, integrating client
accounts and credentials when selling).

## Response snapshot

First inventoried the real stack to keep the docs accurate (no invention): pnpm+Turbo monorepo,
apps/web (Next.js 14.2 + React 18), apps/worker (BullMQ 5), packages db (Drizzle 0.31 + Neon +
pgvector, 12 tables: tenants/users/payerRules/documents/claims/denials/appeals/outcomes/
auditEvents/subscriptions/analyticsEvents/practiceInvites), ai (classifier/extractor/embedder/
drafter/retriever/grounding/deidentifier, openai 4.52), parsers (ERA 835), shared (carc-rarc/
deadline/roi/pilot), security, evals. Services: Clerk 5, Stripe 15, Redis (ioredis), OpenAI/
Azure OpenAI, S3 (@aws-sdk 3), Sentry, PostHog. Tiers: Solo $149/Practice $449/Group $1200.

Created:
1. portfolio_description.md — problem, end-to-end workflow (ingest→diagnose→draft→deadline→
   approve/track/outcome), audience, HIPAA-by-design, architecture table, tiers table,
   engineering highlights, status.
2. budget.md — stack cost table, the HIPAA-BAA multiplier (Neon Business ~$700, Clerk/Vercel
   enterprise), per-denial AI cost ($0.005–0.10), three scenarios (dev ~$15–110; small HIPAA
   prod ~$1,050–2,000; scaled ~$2,400–8,000+), one-time costs, unit economics (break-even
   ~3–5 Practice customers), cost-control levers. All flagged as estimates.
3. how_to_integrate.md — two delivery models (multi-tenant SaaS vs dedicated/white-label),
   Model 1 fast path (self-serve + reseller-provision), Model 2 9-step playbook with a
   credentials intake checklist mapping each vendor → env var, deploy steps, data/user
   onboarding, monitoring/DR/handover, selling checklist.

## Outcome

- ✅ Impact: three client-facing/sales/delivery docs grounded in the real codebase.
- 🧪 Tests: N/A (docs).
- 📁 Files: portfolio_description.md, budget.md, how_to_integrate.md
- 🔁 Next prompts: build C2 Dockerfiles to make Model 2 deploy runnable; optionally a self-serve billing button.
- 🧠 Reflection: corrected the misspelled filename (how_to_ingetgrate → how_to_integrate) and noted it in-file.

## Evaluation notes (flywheel)

- Failure modes observed: none.
- Graders run and results (PASS/FAIL): N/A
- Prompt variant (if applicable): N/A
- Next experiment (smallest change to try): confirm current vendor pricing before quoting a client.
