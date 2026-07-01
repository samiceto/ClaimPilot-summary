# Spec — Part 10: Quality Standards (ClaimPilot)

**Functional Requirements**
- Tests: unit (parsers, CARC/RARC mapping, deadline computation, citation linking), integration (ingest/retrieval/draft/track), e2e (ingest→diagnose→draft→approve→track→outcome), deadline-engine reliability, load, HIPAA control tests. Synthetic PHI only.
- AI golden-set evals in CI: classification, extraction, citation correctness, hallucination rate, appeal quality.

**Non-functional Requirements**
- 99.9% uptime; RPO ≤ 15 min, RTO ≤ 4h; grounding + HIPAA + deadline adherence as release gates.

**User Stories**
- As a customer, I trust updates never fabricate clinical claims, miss deadlines, or leak PHI.

**Acceptance Criteria**
- CI blocks on failing grounding/citation/classification evals.
- Fabricated clinical/coverage claim, auto-submit without approval, or missed deadline = P0, blocked.
- PHI never in logs/analytics (verified each release).

**Technical Constraints**
- PostHog (PHI-free) + Sentry (scrubbed) + PHI-safe logs; dashboards on recovery/$ recovered/deadline adherence/win rate.

**Edge Cases**
- Eval regression → block deploy; PHI detected in logs → P0; deadline-engine failure → alert + fallback.

**Success Metrics**
- Hallucination ≈0; citation correctness high; 0 missed deadlines; 0 PHI leaks; uptime 99.9%.
