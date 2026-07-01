# Spec — Part 3: Business Model (ClaimPilot)

**Functional Requirements**
- Stripe billing per provider/location: Solo $149 / Practice $449 / Group $1,200; reseller volume tier; optional performance fee on recovered claims.
- Enforce tier limits (providers, volume, API, SSO) + upgrade prompts.
- Performance-fee calc from verified recovered claims (opt-in), auditable.

**Non-functional Requirements**
- Billing accuracy 100%; gross margin > 82%; performance-fee attribution auditable.

**User Stories**
- As a buyer, I want pricing that recovers more than it costs.
- As a reseller, I want volume pricing + consolidated billing.

**Acceptance Criteria**
- Provider-based billing + reseller tier work; performance fee only on verified recoveries; over-limit prompts upgrade.

**Technical Constraints**
- Stripe per-seat/provider + metered; performance-fee tied to Outcome + audit log.

**Edge Cases**
- Recovery reversed/recouped by payer → reverse performance fee; provider count change → prorate.

**Success Metrics**
- CAC payback < 7 mo; NRR > 115%; ACV ~$6,000; AI COGS < 10%.
