# Spec — Part 1: Vision & Mission (ClaimPilot)

**Functional Requirements**
- Enforce HIPAA-first + grounded-appeals + human-approval as product-wide invariants.
- Surface north-star (recovered revenue, appeal win rate) on dashboard.

**Non-functional Requirements**
- HIPAA, grounding, and human-approval are enforceable gates, not aspirational.

**User Stories**
- As a practice owner, I trust appeals because they cite real rules and I approve them.
- As a manager, I want proof of recovered cash.

**Acceptance Criteria**
- No appeal submitted without approval; every appeal cited; dashboard shows recovered $ + win rate.

**Technical Constraints**
- Grounding + approval enforced in pipeline + eval/control gates (Parts 6/7/10).

**Edge Cases**
- Empty KB / unknown payer → request info, no fabricated appeal.

**Success Metrics**
- 100% appeals approved + cited; recovered-revenue metric visible per practice.
