---
id: 005
title: Pilot flow — CARC-grounded recoverable-$ estimate (T11.3)
stage: green
date: 2026-06-26
surface: agent
model: claude-opus-4-8
feature: pilot-flow
branch: main
user: samiullahmalik7474@gmail.com
command: (continuation — "T11.3 do this")
labels: ["T11.3", "pilot", "roi", "carc-grounded", "deterministic"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
  - packages/shared/src/pilot.ts
  - packages/shared/src/index.ts
  - packages/shared/src/__tests__/pilot.test.ts
  - apps/web/src/app/api/pilot/estimate/route.ts
tests:
  - "packages/shared pilot.test.ts — 4/4 pass"
  - "shared + web tsc --noEmit clean"
---

## Prompt

"T11.3 do this" — build the pilot flow (recover on a sample batch to prove ROI).

## Response snapshot

`summarizePilotBatch(denials)` in `@claimpilot/shared`: deterministic, CARC-grounded estimate of
recoverable $ over a sample denial batch. Per line, recoverable = deniedAmount ×
win-probability(appealability), where appealability + category come from the unit-tested CARC
reference (consistent with the classification-grounding decision). Reports totalDenials,
appealableDenials, total/appealable denied $, estimatedRecoverable, recoveryRate, and a by-category
breakdown sorted by recoverable $. No LLM/DB → auditable, reproducible — the number a prospect signs
off on. Exposed via `POST /api/pilot/estimate`. 4/4 unit tests (sample batch, empty, negative-amount
clamping, unknown-CARC→medium/appealable).

## Outcome

- ✅ Impact: pilot can quantify recoverable $ on a real denial batch to prove ROI.
- 🧪 Tests: 4/4 unit pass; tsc clean.
- 📁 Files: pilot.ts + export + test + API route.
- 🔁 Next prompts: UI page to upload/select a sample batch and render the summary.
- 🧠 Reflection: keeping it deterministic (vs. calling the classifier) makes the ROI claim
  defensible and free to run at sales scale.

## Evaluation notes (flywheel)

- Failure modes observed: none.
- Graders run and results (PASS/FAIL): recoverable-$ math PASS; category sort PASS; edge cases PASS.
- Prompt variant: n/a.
- Next experiment: feed a parsed real 835 sample through the parser → summarizePilotBatch end-to-end.
