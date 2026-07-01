# Plan — Part 2: Customer Profile (ClaimPilot)

**Objectives:** Specialty/payer-aware onboarding + reseller multi-practice console + clinician-approval roles.

**Architecture Decisions:** Onboarding wizard (specialty/payers/providers); reseller tenant-membership model; RBAC with clinician approver.

**Dependencies:** Auth/RBAC (Part 7), payer KB (Part 4).

**Milestones:** M1 onboarding wizard; M2 RBAC + clinician approver; M3 reseller console; M4 fit check.

**Timeline:** ~2 weeks.

**Risks:** reseller access complexity; onboarding friction in regulated context.

**Mitigation:** scoped membership model; minimal-necessary defaults; guided setup.

**Resource Estimates:** 1 FE + 1 BE, ~2 weeks.
