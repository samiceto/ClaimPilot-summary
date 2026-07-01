# Spec — Part 2: Customer Profile (ClaimPilot)

**Functional Requirements**
- Onboarding captures specialty, payers, providers/locations, volume; reseller (billing-company) multi-practice setup.
- Roles: Owner, Manager, Biller, Clinician/Approver, Viewer; clinician approval for clinical content.

**Non-functional Requirements**
- Onboarding minimal-friction; reseller console manages many practices; minimum-necessary access.

**User Stories**
- As a billing company, I manage many client practices from one console.
- As a clinician, I approve appeals with clinical content.

**Acceptance Criteria**
- Reseller manages multiple practices with scoped access; clinician approval enforced where required.

**Technical Constraints**
- Multi-tenant RLS; reseller scoping; role-based permissions.

**Edge Cases**
- Anti-persona (hospital/cash-only) flagged; provider count changes mid-cycle handled.

**Success Metrics**
- Activation > 55%; reseller-sourced share growing.
