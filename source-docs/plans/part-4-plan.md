# Plan — Part 4: Product Requirements (ClaimPilot)

**Objectives:** Ship core MVP: ingest 835/EOB → diagnose → draft appeal → approve → track → outcome + analytics.

**Architecture Decisions:** X12 835 + PDF EOB parsing pipeline; denial classification; payer-rules RAG drafting w/ citations; deadline engine; approval/e-sign; analytics.

**Dependencies:** Architecture/HIPAA (Parts 5/7), AI (Part 6), data (Part 8).

**Milestones:** M1 ingestion + parse (835/EOB); M2 denial classification + diagnosis; M3 payer-rules KB + retrieval; M4 appeal drafting w/ citations; M5 approval + tracking + deadline engine; M6 analytics; M7 outcome capture.

**Timeline:** ~6 weeks (critical path of 10–12 wk HIPAA MVP).

**Risks:** parse accuracy across payers; citation correctness; deadline reliability.

**Mitigation:** broad 835/EOB corpus tests; citation + deadline as release gates; redundant deadline alerting.

**Resource Estimates:** 2 BE + 1 FE, ~6 weeks.
