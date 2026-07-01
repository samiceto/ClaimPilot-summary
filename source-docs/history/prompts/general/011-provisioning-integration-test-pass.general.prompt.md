---
id: 011
title: Provisioning integration test passes on Neon
stage: general
date: 2026-06-26
surface: agent
model: claude-opus-4-8
feature: none
branch: none
user: samiullahmalik7474@gmail.com
command: general
labels: ["reseller", "provisioning", "integration-test", "neon", "verification"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
 - remaining.md
tests:
 - "reseller-provisioning.integration.test.ts → 4/4 PASS against live Neon (RUN_DB_INTEGRATION=1)"
---

## Prompt

Run the reseller-provisioning integration test against the Neon DB.

## Response snapshot

Ran the gated integration test against live Neon. First run failed at setup, not on logic:
`neon()` rejected the connection string as "not a valid URL". Root cause was MY shell
extraction — `.env` stores `DATABASE_URL='…'` in **single quotes**, and my `sed` only
stripped double quotes, so the URL was passed with literal `'` quotes. Fixed the strip
(`s/^['\"]//;s/['\"]$//`) and re-ran.

Result: **4/4 pass** (10.4s test time):
1. creates a child practice under the reseller with tier limits (maxProviders 10,
   maxMonthlyDenials 1000, subscriptionStatus trialing)
2. refuses to create a practice for a non-reseller tenant
3. invites a member (idempotent on a pending invite) + rejects foreign practices
4. binds an invited signup to the practice with the invited role; uninvited signup
   gets a fresh solo tenant

All synthetic rows cleaned up on teardown. Updated remaining.md T11.1 to record the pass.

## Outcome

- ✅ Impact: T11.1 provisioning flow verified end-to-end on live Neon; closes the provisioning verification debt. Only the Stripe checkout webhook path remains for T11.1.
- 🧪 Tests: reseller-provisioning.integration.test.ts 4/4 PASS.
- 📁 Files: remaining.md (status updated)
- 🔁 Next prompts: walk through real Stripe checkout (browser + test card) so I can verify webhook→DB activation; then T10.5 DR drill.
- 🧠 Reflection: `.env` single-quote wrapping bit the manual extraction — prefer dotenv/loadEnvFile over shell `grep|cut|sed` for connection strings.

## Evaluation notes (flywheel)

- Failure modes observed: single-quoted .env value mangled by double-quote-only sed → neon() URL parse error masquerading as a connectivity failure.
- Graders run and results (PASS/FAIL): integration suite PASS (4/4)
- Prompt variant (if applicable): N/A
- Next experiment (smallest change to try): add a vitest setupFile that runs process.loadEnvFile() so integration tests don't depend on inline env extraction.
