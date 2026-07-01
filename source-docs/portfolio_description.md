# ClaimPilot — AI Denial Management & Appeal Automation for Medical Practices

> **Win back denied insurance claims.** ClaimPilot ingests a practice's denied claims,
> diagnoses the root cause of each denial, and drafts payer-specific appeal letters with the
> correct supporting citations — so practices recover revenue they already earned, without
> drowning their billing staff in paperwork.

---

## The problem it solves

In US healthcare, **5–15% of claims are denied** on first submission, and the majority are
never appealed because the work is manual, deadline-bound, and tedious. Every un-appealed,
appealable denial is **revenue the practice earned but never collected**. Billing teams don't
have the hours to read every CARC/RARC code, look up each payer's appeal rules, and write a
tailored letter before the filing deadline expires.

ClaimPilot automates that entire loop.

---

## What it does (end-to-end workflow)

1. **Ingest** — A practice uploads an insurer remittance file (EDI **835 / ERA**). ClaimPilot
   parses it into structured claims and denials, capturing the denial reason codes (CARC/RARC),
   denied amounts, and dates.
2. **Diagnose** — Each denial is classified by root cause across **16 denial categories** with a
   graded **appealability** score and a success-likelihood estimate. Classification is
   **grounded in a deterministic, unit-tested CARC/RARC reference** — the category and
   appealability come from auditable rules, not a guessing model — while the AI fills the
   judgment fields (recommended action, missing info, reasoning, confidence).
3. **Draft** — For appealable denials, ClaimPilot retrieves the relevant **payer-specific rules**
   (semantic search over a vector knowledge base) and drafts a tailored appeal letter. A
   **citation-grounding gate** refuses to fabricate: if the supporting evidence is too weak, it
   returns *needs-info* instead of hallucinating a justification.
4. **Never miss a deadline** — Filing deadlines are computed per payer and surfaced with
   tiered reminders so appeals go out before they expire.
5. **Approve → Track → Outcome** — Staff review and approve drafts; ClaimPilot tracks the appeal
   to its outcome and feeds results back into analytics (win rate, dollars recovered).
6. **Measure** — A dashboard reports recovered dollars, win rate, open denials, and pending
   appeals; an ROI calculator and a pilot-batch estimator quantify the value before and after.

---

## Inside the app (what users actually see)

ClaimPilot is a full web application, not just a pipeline. The interface is built for a busy
billing team — every screen drives toward the next recoverable dollar.

- **Guided onboarding** — A 3-step wizard captures the practice's specialties, primary payers,
  and provider volume so appeal rules and estimates are tailored from day one.
- **North-star dashboard** — Headline cards for **Revenue Recovered**, **Appeal Win Rate**,
  **Total Denials**, and **Pending Approval**, plus one-click quick actions (import a remittance,
  review pending appeals, manage payer rules) and built-in tools (pilot estimator, ROI calculator).
- **One-click demo mode** — An empty dashboard can load **synthetic (non-PHI) sample data** —
  denials, appeals, and recovered revenue — so a prospect can see the product fully populated in
  seconds without touching real patient data.
- **Self-service intake** — An import screen accepts an **835 ERA**, an **EOB** (paste text), or a
  **manual free-text denial**; users upload a file or paste segments, with a "load sample 835"
  button for instant trials. The worker parses, diagnoses, and queues the appeal draft.
- **Denials workspace** — A filterable, paginated list (by status) with color-coded
  **appealability** and status badges; each denial opens to a full diagnosis view (CARC/RARC,
  category, success-likelihood, recommended action, and the linked appeal letter + outcome).
- **Appeals workspace** — A list with **color-coded deadline urgency** ("7d left", amber/red,
  "OVERDUE") so nothing expires unnoticed. Each appeal shows the drafted letter and a
  **citations panel** that surfaces the exact quoted payer-rule text the letter is grounded in.
- **Identity-bound approval & e-signature** — Appeals are approved behind an **attestation
  checkbox** and a **typed signature that must match the signed-in user**; the server re-validates
  it and records the approval against that account in the tamper-evident audit log. From there,
  staff mark the appeal submitted and **record the payer's outcome** (won / partial / lost /
  withdrawn, recovered amount, reference #, decision date, notes).
- **Role-aware UI** — Approve, submit, and record-outcome actions are gated by role
  (owner / manager / biller / clinician / viewer); the UI only shows what a given user may do, and
  the API re-checks on every call.
- **Payer-rules knowledge base** — A browsable table of the rules used to ground citations,
  clearly scoped **Global** vs **Practice**, reinforcing that appeals only cite real, retrievable
  policy — never an invented one.
- **Reseller console** — A consolidated multi-practice rollup with inline actions to **create a
  child practice**, **invite team members by role**, and **start a reseller-billed subscription**
  via Stripe Checkout.
- **Public ROI calculator & pilot estimator** — A pre-signup calculator (sliders for volume,
  denied amount, current appeal rate, and plan → projected annual recovery and ROI multiple) and a
  pilot tool that turns a pasted denial batch into a CARC-grounded recoverable-dollar estimate.
- **Responsive, premium chrome** — A dark-navy app shell over a light textured surface, fully
  responsive with a collapsible mobile nav, so the app is usable on a laptop or a phone at the desk.

---

## Who it's for

- **Independent & group medical/dental/behavioral-health practices** that lose revenue to
  un-appealed denials.
- **Billing companies & resellers** who manage many practices — ClaimPilot has a built-in
  **reseller console** with a consolidated multi-practice rollup (per-practice win rate,
  recovered $, billing status) and **consolidated billing** (one payment relationship, many
  practices).

---

## Why it's trustworthy (HIPAA & safety by design)

ClaimPilot handles Protected Health Information (PHI), so compliance is built into the
architecture, not bolted on:

- **PHI encryption at rest** (AES-256-GCM) with integrity/auth-tag tamper detection.
- **De-identification** before any data leaves controlled boundaries.
- **Tamper-evident audit log** — a hash-chained audit trail with a `verifyAuditChain()`
  integrity check that detects tampering, forgery, or deletion.
- **Identity-bound approvals** — every appeal approval requires an attestation and a typed
  signature that must match the authenticated user, recorded against their account — so each
  submission has a verifiable human owner of record.
- **Row-Level Security (RLS)** for hard multi-tenant isolation — one practice can never see
  another's data.
- **PHI-free logging** — an automated scanner keeps PHI out of application logs.
- **No PHI to non-covered services** — billing (Stripe) and analytics never receive PHI; the
  AI layer runs behind a BAA-eligible adapter (zero-retention/BAA endpoints).
- **Quality gates in CI** — automated evals for classification accuracy, hallucination, and
  citation correctness must pass before release.

---

## Architecture & technology

A modern, production-grade TypeScript stack in a single monorepo:

| Layer | Technology |
|---|---|
| **Web app** | Next.js 14 (App Router) + React 18, server components |
| **Background worker** | Node.js + **BullMQ** queues (ingest → diagnose → draft → deadline-check) |
| **Database** | **Neon** serverless Postgres + **Drizzle ORM**, with **pgvector** for semantic retrieval |
| **Auth** | **Clerk** (multi-tenant, role-based: owner / manager / biller / reseller) |
| **Billing** | **Stripe** (subscriptions, consolidated reseller billing, webhooks) |
| **Queues / cache** | **Redis** |
| **AI** | OpenAI / **Azure OpenAI** (configurable models for extraction, classification, embeddings, appeal drafting) |
| **PHI storage** | S3-compatible object storage (encrypted) |
| **Observability** | Sentry (errors) + PostHog (product analytics) + a public `/api/health` readiness probe |
| **Tooling** | pnpm + Turbo monorepo, Zod validation, Vitest, GitHub Actions CI |

**Domain logic packages**: a deterministic CARC/RARC reference engine, payer deadline math, an
ROI calculator, and a pilot-batch estimator — all unit-tested and reused across the app and
worker.

---

## Product tiers

| Tier | Price (USD/mo) | Providers | Locations | Denials/mo | SSO |
|---|---|---|---|---|---|
| **Solo** | $149 | 1 | 1 | 100 | — |
| **Practice** | $449 | 10 | 5 | 500 | — |
| **Group** | $1,200 | Unlimited | Unlimited | Unlimited | ✓ |

Resellers can provision child practices under their own account with consolidated billing.

---

## Engineering highlights

- **Grounded AI, not a chatbot** — denial categories and appealability come from a deterministic,
  auditable reference; the LLM only fills judgment fields. The appeal drafter has a hard
  citation-grounding gate that refuses to fabricate.
- **Verified end-to-end** — the full ingest → diagnose → draft → approve → track → outcome
  pipeline is exercised by integration tests against a live database, including a reseller
  provisioning flow and real semantic retrieval over the payer knowledge base.
- **Real bugs caught by tests** — e.g. a jsonb retrieval-filter operator bug that broke every
  CARC-filtered query in production, a deadline-reminder duplicate-firing bug, and an audit-hash
  integrity defect — all fixed and regression-tested.
- **Disaster recovery** — documented DR runbook with RPO ≤ 5 min / RTO ≤ 60 min targets using
  Neon point-in-time recovery.

---

## Status

Core product is built and tested: ingestion, AI diagnosis, grounded appeal drafting, deadline
tracking, dashboards, the reseller console with provisioning, and Stripe billing. Remaining work
is production deployment hardening and go-to-market (content, partnerships, optional PM/EHR
integrations).
