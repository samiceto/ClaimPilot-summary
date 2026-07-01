---
id: 010
title: Complete coding verification and human handoff
stage: general
date: 2026-06-26
surface: agent
model: claude-opus-4-8
feature: none
branch: none
user: samiullahmalik7474@gmail.com
command: general
labels: ["verification", "type-check", "stripe", "dr-drill", "handoff"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
 - packages/db/src/migrate.ts
tests:
 - "pnpm --filter @claimpilot/db --filter @claimpilot/web type-check → exit 0 (both Done)"
 - "reseller-provisioning.integration.test.ts → NOT run (DB-write blocked by auto-mode classifier)"
---

## Prompt

okey now complete the coding task and then tell me step by step i will walk you through all process needs human to complete

## Response snapshot

Completed the codeable/verifiable portion without touching the live DB:
- `db` + `web` type-check both pass (exit 0) — confirms migrate.ts DX fix and all
  provisioning/billing/reseller-console/pilot code compiles.
- Attempted the provisioning integration test again; auto-mode classifier blocked it
  ("complete the coding task" is not DB-targeted authorization). Did not retry further.

Then produced a step-by-step human walkthrough for the four human-only processes:
A. Authorize + run the provisioning integration test (needs explicit DB phrase).
B. Real Stripe checkout → webhook → DB activation (stripe CLI listen on
   /api/webhooks/stripe, test card 4242, tier checkout from /reseller or /billing).
C. DR drill via Neon PITR + post-restore checklist (verifyAuditChain, PHI decrypt, RPO/RTO).
D. Sentry alert rules + uptime monitor on public PHI-free GET /api/health.

Key facts surfaced for the walkthrough: webhook route `/api/webhooks/stripe` handles
customer.subscription.created/updated/deleted, keys off `sub.metadata.tenant_id`, sets
`tenants.subscriptionStatus = sub.status`. Checkout routes: `/api/billing/checkout` +
`/api/reseller/practices/[id]/subscription`. Env: STRIPE_SECRET_KEY, STRIPE_WEBHOOK_SECRET,
STRIPE_PRICE_{SOLO,PRACTICE,GROUP}. Runbook: docs/runbooks/disaster-recovery.md.

## Outcome

- ✅ Impact: code health verified (type-check green); clear human-handoff plan delivered.
- 🧪 Tests: type-check exit 0; integration test still blocked on DB authorization.
- 📁 Files: packages/db/src/migrate.ts (prior fix, re-verified compiling)
- 🔁 Next prompts: explicit "run the reseller-provisioning integration test against the Neon DB"; then walk through Stripe checkout together.
- 🧠 Reflection: classifier consistently requires DB-specific phrasing; stopped retrying after 3 denials and pivoted to safe verification + handoff.

## Evaluation notes (flywheel)

- Failure modes observed: foreground `sleep` blocked by harness; switched to Monitor until-loop for the slow `/mnt/d` type-check.
- Graders run and results (PASS/FAIL): type-check PASS
- Prompt variant (if applicable): N/A
- Next experiment (smallest change to try): pre-build `@claimpilot/*` to dist/ to cut the ~3.5min tsx/type-check cold start.
