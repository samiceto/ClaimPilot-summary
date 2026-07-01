---
id: 007
title: Fix Next.js production build (workspace TS resolution)
stage: green
date: 2026-06-26
surface: agent
model: claude-opus-4-8
feature: none
branch: main
user: samiullahmalik7474@gmail.com
command: continue
labels: ["build", "nextjs", "webpack", "monorepo", "deployment"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
 - apps/web/next.config.mjs
 - remaining.md
tests:
 - "pnpm --filter @claimpilot/web build (next build) → Compiled successfully, 25/25 pages"
---

## Prompt

continue

(Continuation of the autonomous "complete the remaining / turn marked-done into
verified-working" mandate. While verifying that the new reseller/pilot/health/billing
routes compile, the Next.js production build was found to be broken — a pre-existing,
deployment-blocking failure unrelated to this session's feature work — and had to be fixed.)

## Response snapshot

`next build` failed with `Module not found: Can't resolve './types.js'` (also
`./constants.js`, `./carc-rarc.js`, `./deadline.js`, `./roi-calculator.js`) emitted from
`@claimpilot/shared/src/index.ts`, triggered by the existing `/roi` page importing the package.

Root cause: the workspace packages (`@claimpilot/{shared,db,ai,parsers,security}`) ship **TS
source** — `main → ./src/index.ts` with no `dist/` — and use NodeNext-style `.js` import
specifiers that resolve to sibling `.ts` files at build time. `next.config.mjs` had neither
`transpilePackages` (so Next treated them as opaque externals) nor a webpack rule to map
`.js`→`.ts`, so module resolution failed.

Fix (two parts, both required — `transpilePackages` alone still failed):
1. `transpilePackages: ["@claimpilot/shared","@claimpilot/db","@claimpilot/ai","@claimpilot/parsers","@claimpilot/security"]`
2. webpack `config.resolve.extensionAlias = { ".js": [".ts",".tsx",".js"], ".mjs": [".mts",".mjs"] }`

Rebuild → `✓ Compiled successfully`, `✓ Generating static pages (25/25)`. All new routes present
in the manifest: `/api/health` (static), `/api/billing/checkout`, `/api/pilot/estimate`,
`/api/reseller/practices` (+ `/[practiceId]/invites`, `/[practiceId]/subscription`), `/pilot`,
`/reseller`.

## Outcome

- ✅ Impact: Resolved a pre-existing deployment blocker; the web app now produces a clean
  production build, and every new feature route compiles.
- 🧪 Tests: `next build` → Compiled successfully (25/25 pages, exit 0).
- 📁 Files: `apps/web/next.config.mjs` (transpilePackages + webpack extensionAlias), `remaining.md` (logged the fix).
- 🔁 Next prompts: apply migration 0002 (needs explicit user authorization) → run reseller-provisioning integration test; verify a real test-card checkout activates a subscription via the Stripe webhook.
- 🧠 Reflection: Shipping packages as raw TS `src` is convenient for `tsx`/Vitest but pushes the
  resolution burden onto every consumer's bundler. Pre-building to `dist/` (also flagged for worker
  cold-start) would remove both the `extensionAlias` hack and the slow `tsx` transpile.

## Evaluation notes (flywheel)

- Failure modes observed: `transpilePackages` alone did not fix `.js`→`.ts` resolution; the webpack `extensionAlias` was the decisive part.
- Graders run and results (PASS/FAIL): `next build` PASS.
- Prompt variant (if applicable): n/a
- Next experiment (smallest change to try): build the workspace packages to `dist/` and point `main`/`exports` there to drop the bundler workaround.
