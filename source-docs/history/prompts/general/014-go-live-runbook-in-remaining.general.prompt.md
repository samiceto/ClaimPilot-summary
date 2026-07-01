---
id: 014
title: Add go-live runbook to remaining.md
stage: general
date: 2026-06-27
surface: agent
model: claude-opus-4-8
feature: none
branch: none
user: samiullahmalik7474@gmail.com
command: general
labels: ["runbook", "deployment", "stripe", "hipaa", "docs"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
 - remaining.md (appended "▶ GO-LIVE RUNBOOK (human steps)" section)
tests:
 - none (docs change)
---

## Prompt

okey now your task is to update the remaining.md file with all steps remaining from "Your next moves" (Stripe checkout verification), with all next, and then steps to deploy i will follow and get my product live, mention each step.

## Response snapshot

Inspected the deploy surface first: NO deploy config exists (no Dockerfile/vercel.json/fly.toml,
only ci.yml). Stack = Next.js web + separate BullMQ worker + Redis + Neon + S3-compatible PHI
storage, under HIPAA/BAA constraints. Enumerated the real prod env vars from `.env`.

Appended a self-contained "▶ GO-LIVE RUNBOOK (human steps)" to remaining.md with checkboxes and
[Claude] hand-back markers:
- PART A — local Stripe billing verification (A1 restart → A2 stripe listen → A3 sign up →
  A4 console checkout → A5 test card → A6 webhook 200 → A7 [Claude] verify DB activation).
  Noted Option C (no self-serve billing UI; offer to add a dashboard button).
- PART B — remaining ops: DR drill (B1), uptime monitor on /api/health (B2), Sentry alerts (B3).
- PART C — production deploy: C1 BAAs + fresh PHI key; C2 [Claude] build Dockerfiles for web
  (transpilePackages done) + worker (must pre-build packages to dist, no runtime tsx),
  recommend Azure Container Apps (existing Azure OpenAI usage + skill) with AWS/Fly/Render
  alternatives, Vercel only for web; C3 managed Redis + S3 PHI bucket + Neon prod branch;
  C4 Clerk prod instance + Stripe live mode (recreate products, register live webhook → its own
  whsec) + Sentry; C5 full prod env var set; C6 db:migrate prod; C7 deploy + /api/health smoke
  test + live checkout; C8 post-launch DR drill + GTM.

## Outcome

- ✅ Impact: single followable go-live runbook in-repo; clearly separates user steps from [Claude] hand-backs and flags the missing deploy config as a build task.
- 🧪 Tests: N/A (docs).
- 📁 Files: remaining.md
- 🔁 Next prompts: user runs PART A → reports sign-up email for DB verification; or asks Claude to build C2 deploy artifacts / the Option C billing button.
- 🧠 Reflection: surfaced the honest gap — "deploy" here means first authoring Dockerfiles + choosing a BAA host, not a one-click push.

## Evaluation notes (flywheel)

- Failure modes observed: none.
- Graders run and results (PASS/FAIL): N/A
- Prompt variant (if applicable): N/A
- Next experiment (smallest change to try): build the web + worker Dockerfiles so PART C2 is unblocked.
