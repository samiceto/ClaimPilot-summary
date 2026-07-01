---
id: 004
title: Reseller provisioning + consolidated Stripe billing (T11.1)
stage: green
date: 2026-06-26
surface: agent
model: claude-opus-4-8
feature: reseller-console
branch: main
user: samiullahmalik7474@gmail.com
command: (continuation — user authorized Stripe + provisioning)
labels: ["T11.1", "reseller", "provisioning", "stripe", "billing", "migration", "invites"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
  - packages/db/src/schema.ts
  - packages/db/migrations/0002_tricky_demogoblin.sql
  - apps/web/src/lib/reseller-provisioning.ts
  - apps/web/src/lib/billing.ts
  - apps/web/src/app/api/reseller/practices/route.ts
  - apps/web/src/app/api/reseller/practices/[practiceId]/invites/route.ts
  - apps/web/src/app/api/reseller/practices/[practiceId]/subscription/route.ts
  - apps/web/src/app/api/billing/checkout/route.ts
  - apps/web/src/app/api/webhooks/clerk/route.ts
  - apps/web/scripts/setup-stripe-products.ts
  - .env
tests:
  - "Stripe smoke: created real checkout sessions for solo/practice/group (test mode), cleaned up"
  - "db + web tsc --noEmit clean"
  - "reseller-provisioning.integration.test.ts — written, gated; runs after migration 0002 applied"
---

## Prompt

"you have stripe secret key in .env so you can generate the stripe product etc … Provisioning (no
Stripe) … T11.3 do this" — user authorized creating Stripe products + building reseller provisioning
and billing.

## Response snapshot

- **Provisioning**: new `practice_invites` table + `invite_status` enum (migration `0002`,
  partial-unique on pending `(email, tenant)`). `lib/reseller-provisioning.ts` —
  `createChildPractice` (per-tier capacity limits), `inviteToPractice` (child-scope-guarded,
  idempotent on a pending invite), `acceptInviteOrProvision` (Clerk `user.created` now binds an
  invited email to the practice with the invited role, else auto-provisions a solo tenant). APIs:
  `POST /api/reseller/practices`, `POST /api/reseller/practices/[id]/invites`.
- **Billing (Stripe TEST mode)**: `scripts/setup-stripe-products.ts` idempotently created the 3 tier
  products + monthly prices ($149/$449/$1200) from `BILLING_TIERS`; wrote `STRIPE_PRICE_*` into
  `.env`. `lib/billing.ts` `ensureStripeCustomer` + `createCheckoutSession` implements
  **consolidated reseller billing** — a child's subscription is billed to the reseller's Stripe
  customer, tagged `metadata.tenant_id`, which the existing webhook activates per-tenant. Routes:
  `POST /api/billing/checkout` (self-serve) + `POST /api/reseller/practices/[id]/subscription`.
- **Verified**: smoke test created real checkout sessions for all 3 tiers against the live Stripe
  test API and cleaned up the ephemeral customer; db + web type-check clean.

## Outcome

- ✅ Impact: reseller can provision child practices, invite members, and start consolidated
  (reseller-billed) subscriptions; Stripe catalog now exists.
- 🧪 Tests: Stripe smoke PASS (3/3 tiers); tsc clean; provisioning integration test ready (gated on
  migration `0002`).
- 📁 Files: 1 schema+migration, 2 libs, 5 routes, 1 webhook refactor, 1 setup script, .env.
- 🔁 Next prompts: apply migration `0002` → run provisioning IT; `/reseller` create/invite/subscribe
  UI; confirm webhook→DB activation via a real test-card checkout.
- 🧠 Reflection: consolidated billing falls out naturally from one Stripe customer holding many
  `tenant_id`-tagged subscriptions — no separate invoicing engine needed.

## Evaluation notes (flywheel)

- Failure modes observed: `db:migrate` correctly blocked by the safety classifier (live-DB change) —
  surfaced to the user for explicit approval rather than worked around.
- Graders run and results (PASS/FAIL): Stripe session creation PASS; type-check PASS.
- Prompt variant: n/a.
- Next experiment: a webhook unit test feeding a synthetic `customer.subscription.created` to assert
  the tenant tier/limits update, decoupled from a live checkout.
