---
id: 002
title: Retrieval/KB integration + grounded draft (T10.1)
stage: green
date: 2026-06-26
surface: agent
model: claude-opus-4-8
feature: payer-rules-retrieval
branch: main
user: samiullahmalik7474@gmail.com
command: (continuation — "go ahead and complete the remaining")
labels: ["T10.1", "rag", "pgvector", "retrieval", "drafting", "bugfix", "integration-test"]
links:
  spec: null
  ticket: null
  adr: null
  pr: null
files:
  - packages/ai/src/retriever.ts
  - packages/ai/src/__tests__/retrieval-draft.integration.test.ts
  - remaining.md
tests:
  - "packages/ai retrieval-draft.integration.test.ts — 2/2 pass (real pgvector + real OpenAI, RUN_AI_INTEGRATION=1)"
  - "live-DB probe: carc_codes filter variants (jsonb @> text[] FAIL; ?| OK; @> jsonb OK)"
---

## Prompt

continue (resume T10.1 retrieval + KB per the user's selection: seed payer_rules with real
OpenAI embeddings -> retrievePayerRules() integration test -> real draftAppeal grounded in
retrieved rules -> appeal reaches ready_for_review/pending_approval, not needs_info). User
directive in effect: "go ahead and complete the remaining, tell me when you need my manual
interference."

## Response snapshot

Built the T10.1 retrieval/KB integration slice and, in the process, found and fixed a real
production bug.

- **Bug (production):** `retriever.ts` filtered the `carc_codes` jsonb string-array column with
  `carc_codes @> ARRAY[$codes]::text[]`. Postgres rejects this outright
  (`operator does not exist: jsonb @> text[]`), confirmed against the live DB. The appeal-draft
  processor *always* passes `carcCodes: [denial.carcCode]`, so every CARC-filtered retrieval was
  throwing. Fixed with the jsonb-native membership operator: `carc_codes ?| ARRAY[$codes]::text[]
  OR carc_codes = '[]'::jsonb` (empty array = payer-wide rule).
- **Integration test:** `packages/ai/src/__tests__/retrieval-draft.integration.test.ts`, gated on
  `DATABASE_URL` + `OPENAI_API_KEY` + `RUN_AI_INTEGRATION=1` (skips cleanly in CI without them).
  Seeds 3 synthetic *global* (tenant_id NULL) payer rules under a unique `TEST-PAYER-*` id with
  real OpenAI embeddings, then: (1) asserts `retrievePayerRules` returns the CARC-50 rule + the
  payer-wide empty-carc rule and excludes the CARC-29 decoy, ranked with the medical-necessity rule
  first above threshold; (2) runs real `draftAppeal` and asserts `status === "ready_for_review"`
  with non-empty letter body and every citation's `payerRuleId` bound to a retrieved rule id.
- **Hardening:** retry helper (5x backoff) around insert/delete so transient Neon `ETIMEDOUT`
  blips over WSL never leak seed rows or fail the suite. Cleaned up orphan rows from earlier
  killed/blipped runs (`TEST-PAYER-%` now 0).

## Outcome

- ✅ Impact: closes the last real-AI e2e gap (real-retrieval grounded draft) and completes Part 10
  test pillars T10.1–T10.4; fixes a silent production retrieval failure on every CARC-filtered query.
- 🧪 Tests: 2/2 integration pass with real pgvector + real OpenAI; `@claimpilot/ai` tsc clean.
- 📁 Files: retriever.ts (1-line filter fix + comment), new integration test, remaining.md updates.
- 🔁 Next prompts: T10.5 (dashboards + alerting + DR drill) or Part 11 (reseller console).
- 🧠 Reflection: an isolated, valid-looking SQL string can still be categorically broken at runtime;
  a live-DB probe of the operator was faster and more certain than reasoning about jsonb vs text[].

## Evaluation notes (flywheel)

- Failure modes observed: transient Neon `connect ETIMEDOUT` over WSL (environmental, not logic) on
  ~1/3 of DB calls — mitigated with retry, not by changing assertions.
- Graders run and results (PASS/FAIL): retrieval filter correctness PASS; grounded-draft status
  PASS (ready_for_review); citation grounding invariant PASS.
- Prompt variant (if applicable): n/a (drafter prompt unchanged).
- Next experiment (smallest change to try): add a negative case asserting `needs_info` when no rules
  are retrieved, to pin the grounding gate from both sides.
