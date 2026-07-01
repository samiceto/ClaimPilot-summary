---
id: 013
title: Set Stripe webhook secret in .env
stage: general
date: 2026-06-27
surface: agent
model: claude-opus-4-8
feature: none
branch: none
user: samiullahmalik7474@gmail.com
command: general
labels: ["stripe", "webhook", "env", "billing", "config"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
 - .env (STRIPE_WEBHOOK_SECRET set; duplicate empty definition removed)
tests:
 - "grep ^STRIPE_WEBHOOK_SECRET= .env → exactly 1 line with the whsec value"
---

## Prompt

set the whsec_....

## Response snapshot

User asked to set the Stripe webhook secret but pasted only the placeholder. The Stripe CLI
was already installed (/usr/bin/stripe) and authenticated, so fetched the local webhook
signing secret with `stripe listen --print-secret` (the same secret `stripe listen
--forward-to` uses, so signatures verify).

Found .env had TWO STRIPE_WEBHOOK_SECRET lines: an empty one in the canonical Stripe block
(line 25) and the real value appended at the file end (line 79) — a shadowing hazard since
dotenv duplicate-key resolution is order-dependent. Consolidated: set line 25 to the real
secret and removed the stray end-of-file duplicate. Verified exactly one definition remains.

Reminded the user to restart `pnpm dev` (env read at boot) and that `stripe listen
--forward-to localhost:3000/api/webhooks/stripe` reuses the same secret.

(Secret value intentionally not recorded here.)

## Outcome

- ✅ Impact: webhook signature verification will now succeed; removed a duplicate that could have shadowed the value.
- 🧪 Tests: single STRIPE_WEBHOOK_SECRET line confirmed via grep.
- 📁 Files: .env
- 🔁 Next prompts: user restarts dev + runs `stripe listen`, completes a test-card checkout; I verify tenants.subscriptionStatus in Neon.
- 🧠 Reflection: prefer `stripe listen --print-secret` over asking the user to copy/paste; always de-dupe env keys.

## Evaluation notes (flywheel)

- Failure modes observed: duplicate env key (empty + real) — order-dependent shadowing risk.
- Graders run and results (PASS/FAIL): grep dedupe check PASS.
- Prompt variant (if applicable): N/A
- Next experiment (smallest change to try): a quick `.env` lint that flags duplicate keys.
