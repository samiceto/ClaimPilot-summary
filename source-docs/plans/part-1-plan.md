# Plan — Part 1: Vision & Mission (ClaimPilot)

**Objectives:** Encode HIPAA-first, grounded-appeals, human-approval, and recovered-revenue north-star as enforceable foundations.

**Architecture Decisions:** Approval gate + grounding enforced in appeal pipeline; PHI-safe metrics; north-star dashboard widget.

**Dependencies:** RAG/grounding (Part 6), HIPAA (Part 7), metrics (Part 12).

**Milestones:** M1 invariants spec; M2 approval+grounding gate; M3 north-star widget; M4 CI gates.

**Timeline:** ~1 week (foundational, parallel with Parts 4/6/7).

**Risks:** invariants treated as docs; metric mistrust.

**Mitigation:** make HIPAA/grounding/approval CI + control gates; metrics from verified outcomes.

**Resource Estimates:** 1 FE + 1 BE part-time, week 1.
