---
id: 003
title: Reseller console MVP — consolidated rollup (T11.1)
stage: green
date: 2026-06-26
surface: agent
model: claude-opus-4-8
feature: reseller-console
branch: main
user: samiullahmalik7474@gmail.com
command: (continuation — "complete the remaining"; user selected Part 11 reseller console)
labels: ["T11.1", "reseller", "multi-tenant", "billing", "authz", "rollup", "integration-test"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
  - apps/web/src/lib/reseller.ts
  - apps/web/src/lib/auth.ts
  - apps/web/src/app/api/reseller/practices/route.ts
  - apps/web/src/app/reseller/page.tsx
  - apps/web/src/lib/__tests__/reseller.integration.test.ts
  - apps/web/vitest.config.ts
  - apps/web/package.json
  - remaining.md
tests:
  - "apps/web reseller.integration.test.ts — 3/3 pass (real DB, RUN_DB_INTEGRATION=1)"
  - "@claimpilot/web tsc --noEmit — clean"
---

## Prompt

continue — build Part 11 reseller console (T11.1: reseller console + consolidated billing,
multi-practice management). User directive in effect: "go ahead and complete the remaining, tell
me when you need my manual interference." Selected focus via AskUserQuestion: "Part 11 reseller
console".

## Response snapshot

Shipped the T11.1 reseller-console MVP — a read-only consolidated multi-practice rollup, the
smallest-viable core every later reseller feature builds on. The schema already carried the
scaffolding (`tenants.parentTenantId`, `tenants.isReseller`, `reseller` role, `subscriptions`),
so no migration was needed.

- **`lib/reseller.ts` — `getResellerRollup(db, resellerTenantId)`**: pure, testable data function
  (no Next/Clerk imports). Finds child practices (`parentTenantId = reseller`), then one grouped
  query per metric across all children (no N+1), stitched in memory into per-practice
  denials/appeals/won/win-rate/recovered-$ + billing tier/status. Consolidated totals use a *true*
  consolidated win rate (totalWon/totalAppeals), not an average of per-practice rates. Sorted by
  recovered $ desc.
- **`lib/auth.ts` — `requireReseller()`**: authorizes the console by checking the `isReseller`
  tenant capability + an allow-listed role set directly, NOT via the numeric role hierarchy — which
  ranks `reseller` lowest (0) and could never satisfy `requireRole(["manager","owner"])`. Reseller
  is an orthogonal cross-tenant capability, not a rank. Returns the reseller tenant id whose
  children the caller may read; cross-tenant reads are strictly scoped to that subtree.
- **`api/reseller/practices/route.ts`**: `GET` → requireReseller (401 vs 403 split) →
  getResellerRollup → JSON, with a PHI-free `view`/`reseller_console` audit entry.
- **`app/reseller/page.tsx`**: server-rendered console (consolidated KPI cards + per-practice
  table, dashboard styling), redirects non-resellers to `/dashboard`. Already gated by Clerk
  middleware (not in the public-route list).
- **Testing**: added vitest to `apps/web` (config + `test` script + devDep) and a gated integration
  test (`RUN_DB_INTEGRATION=1`) that seeds a reseller + 2 children + an outsider control through
  real claim→denial→appeal→outcome chains, asserting per-practice numbers, **outsider exclusion**,
  recovered-$ sort, consolidated totals (67% win rate from 2/3), and the empty-reseller case.
  Retry-hardened, dependency-ordered teardown.

## Outcome

- ✅ Impact: T11.1 P0 GTM console MVP usable; reseller can see consolidated performance + billing
  status across all child practices. Surfaced + documented a real authz wrinkle (reseller role rank).
- 🧪 Tests: 3/3 integration pass on live DB; web tsc clean; 0 seed-row leaks after teardown.
- 📁 Files: 1 new lib + 1 authz helper + 1 API route + 1 page + 1 test + vitest wiring; remaining.md.
- 🔁 Next prompts: child-practice provisioning (create practice / invite users) and consolidated
  Stripe billing actions (reseller-level invoicing) — explicitly out of this read-only MVP.
- 🧠 Reflection: keeping the rollup a pure `(db, id) => data` function made it testable without a
  Next.js test harness and reusable by both the API route and the server page.

## Evaluation notes (flywheel)

- Failure modes observed: none in logic; only the known transient Neon ETIMEDOUT over WSL (mitigated
  with retry in setup/teardown).
- Graders run and results (PASS/FAIL): per-practice aggregation PASS; tenant-scope isolation
  (outsider excluded) PASS; consolidated totals PASS; empty-reseller PASS.
- Prompt variant (if applicable): n/a.
- Next experiment (smallest change to try): add a 403 path test for a non-reseller tenant hitting
  requireReseller (needs a Clerk auth stub), to pin the authz boundary from the negative side.
