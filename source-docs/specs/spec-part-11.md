# Spec — Part 11: Growth Strategy (ClaimPilot)

**Functional Requirements**
- Billing-company reseller console (multi-practice management, consolidated billing, scoped access).
- ROI content hub + recovery calculator; pilot/trial on sample batch.
- Specialty-association partnerships; PM/EHR marketplace integrations (post-MVP).
- Activation: import 835/EOB → diagnosed denials + recoverable $ + first appeal.

**Non-functional Requirements**
- Time-to-first-appeal minimized; reseller attribution tracked; HIPAA trust front-and-center in sales.

**User Stories**
- As a billing company, I onboard many practices and grow recovered revenue across them.
- As a buyer, I want to see recoverable $ before committing.

**Acceptance Criteria**
- Reseller manages multiple practices + consolidated billing; pilot demonstrates recovered $.
- Activation reaches first appeal in one session.

**Technical Constraints**
- Reseller scoping via RLS memberships; integration OAuth (post-MVP); HIPAA-compliant onboarding.

**Edge Cases**
- Reseller offboarding → practice continuity; messy legacy ERA imports handled.

**Success Metrics**
- CAC payback < 7 mo; NRR > 115%; activation > 55%; reseller-sourced share growing.
