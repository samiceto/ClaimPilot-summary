# ClaimPilot — Client Integration & Onboarding Playbook

_How to take ClaimPilot from "sold" to "live for a paying client", including wiring up the
client's own accounts and credentials. (Filename corrected from `how_to_ingetgrate.md`.)_

---

## Step 0 — Pick the delivery model

There are two ways to sell ClaimPilot. Choose before you quote.

| | **Model 1 — Multi-tenant SaaS (recommended)** | **Model 2 — Dedicated / white-label deployment** |
|---|---|---|
| **What** | You run ONE ClaimPilot deployment; each client is a *tenant* inside it. | Each client gets their OWN deployment with their OWN accounts/credentials. |
| **Client onboarding** | Minutes — sign-up or reseller-provision. | Hours/days — stand up infra + plug in their credentials. |
| **Who owns the accounts** | You own Neon/Clerk/Stripe/LLM; clients are isolated by Row-Level Security. | The client owns (or co-owns) the accounts; you configure them. |
| **Best for** | SMB practices, fast scaling, lower price. | Enterprise clients who require data/account ownership or their own BAAs. |
| **Per-client cost** | Marginal (shared infra). | Full infra stack per client. |

> The app is built **multi-tenant first** (tenants, RLS, reseller console). Model 1 is the
> default; Model 2 reuses the same code but with per-client credentials in a separate
> environment.

---

## Model 1 — Onboard a client as a tenant (fast path)

No new infrastructure. Two sub-options:

### 1A. Self-serve sign-up
1. Send the client your app URL → they **sign up** (`/sign-up`). Clerk's `user.created` webhook
   auto-provisions a fresh tenant + owner user for them.
2. They subscribe via checkout (Stripe) → tier limits apply automatically.
3. They upload their first **835/ERA** denial file → the pipeline runs.

### 1B. Reseller-provisioned (you/billing-company onboard them)
1. Your tenant must have the **reseller** capability (`tenants.is_reseller = true`).
2. In **`/reseller`** → **"+ New Practice"** → name + tier → creates the child practice under you.
3. Expand the practice → **Invite member** (email + role) → they sign up against the pending
   invite and are bound to that practice.
4. **Start checkout** for the practice → **consolidated billing** (the child's subscription is
   billed to *your* Stripe customer; one payment relationship, many practices).

That's the whole integration for Model 1 — no credentials to wire.

---

## Model 2 — Dedicated deployment with the client's credentials

Use this when the client wants their own accounts, domain, and/or BAAs. Work top to bottom.

### Step 1 — Legal & compliance (do first, blocks go-live)
- [ ] Sign a **BAA** with the client (you are their Business Associate).
- [ ] Ensure **BAAs are in place with every PHI-touching vendor** (hosting, Neon, LLM provider,
      object storage, Redis). _Stripe needs no BAA — never send it PHI._
- [ ] Confirm who owns each cloud account (client-owned vs you-managed).

### Step 2 — Collect the client's credentials (intake checklist)
Give the client this checklist; collect into their secret manager, **never commit to git**.

| Credential | Where the client gets it | Env var(s) |
|---|---|---|
| **Postgres (Neon)** connection strings | Neon console → project → connection details (pooled + unpooled) | `DATABASE_URL`, `DATABASE_URL_UNPOOLED` |
| **Clerk** keys + webhook | Clerk dashboard → API keys (prod instance) | `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY`, `CLERK_SECRET_KEY`, `NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in`, `NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up` |
| **Stripe** keys + webhook secret | Stripe dashboard → Developers → API keys (live) | `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, `STRIPE_PRICE_SOLO/PRACTICE/GROUP` |
| **LLM** key + endpoint | Azure OpenAI resource **or** OpenAI account (BAA-covered) | `AZURE_OPENAI_API_KEY` + `AZURE_OPENAI_ENDPOINT` **or** `OPENAI_API_KEY`; plus `CLAIMPILOT_EMBEDDING/EXTRACTION/SYNTHESIS_MODEL` |
| **Redis** URL | managed Redis (Azure Cache / Upstash / ElastiCache) | `REDIS_URL` |
| **PHI object storage** | S3 / R2 / Azure Blob (S3 API) bucket + keys | `PHI_STORAGE_ENDPOINT`, `PHI_STORAGE_ACCOUNT_ID`, `PHI_STORAGE_BUCKET`, `PHI_STORAGE_ACCESS_KEY_ID`, `PHI_STORAGE_SECRET_ACCESS_KEY` |
| **PHI encryption key** | **you generate** a fresh 32-byte key per client | `PHI_ENCRYPTION_KEY` |
| **Sentry / PostHog** (optional) | their projects | `NEXT_PUBLIC_SENTRY_DSN`, `SENTRY_ORG/PROJECT/AUTH_TOKEN`, `NEXT_PUBLIC_POSTHOG_KEY/HOST` |
| **Domain** | their DNS | `APP_URL=https://<their-domain>` |

> ⚠️ Generate a **unique** `PHI_ENCRYPTION_KEY` per client and store it in their secret manager.
> If it's lost or rotated, previously-encrypted PHI becomes unreadable.

### Step 3 — Build the deploy artifacts (one-time engineering)
> Not yet in the repo (`remaining.md` → PART C2). Hand to engineering.
- [ ] Dockerfile for `apps/web` (`next build` → `next start`; `transpilePackages` already set).
- [ ] Dockerfile for `apps/worker` — pre-build the `@claimpilot/*` packages to `dist`, run
      `node dist/main.js` (do **not** run `tsx` at runtime in prod).
- [ ] Host config (recommended **Azure Container Apps** for web + worker; alternatives Render /
      Fly / AWS ECS). Web-only could use Vercel; the worker always needs a container host.

### Step 4 — Provision infrastructure (per client)
- [ ] Create/confirm the **Neon** production branch (the client's own project for Model 2).
- [ ] Provision **managed Redis**; capture `REDIS_URL`.
- [ ] Provision the **PHI bucket**; capture the `PHI_STORAGE_*` values.

### Step 5 — Configure the client's third-party services
- [ ] **Clerk (prod instance):** add the client's domain; copy `pk_live_…` / `sk_live_…`; point
      the **`user.created` webhook** at `https://<their-domain>/api/webhooks/clerk`.
- [ ] **Stripe (live mode):** run `scripts/setup-stripe-products.ts` against their live key to
      create the 3 tier products/prices → fill `STRIPE_PRICE_*`; register a **live webhook** at
      `https://<their-domain>/api/webhooks/stripe` (events: `customer.subscription.*`,
      `checkout.session.completed`) → copy its signing secret to `STRIPE_WEBHOOK_SECRET`.
- [ ] **LLM:** confirm the BAA-covered endpoint/key and the three model names.

### Step 6 — Set env vars + migrate the database
- [ ] Put the full env set (Step 2) into the host's secret store (`NODE_ENV=production`).
- [ ] Run migrations against the client's DB (env auto-loads now — no `set -a` dance):
      ```bash
      pnpm --filter @claimpilot/db db:migrate
      ```
      Confirms all 12 tables + RLS + `practice_invites` exist.

### Step 7 — Deploy & smoke test
- [ ] Deploy **web** + **worker** containers. Worker should log
      `[Worker] ClaimPilot workers started` and register the 3 BullMQ queues.
- [ ] `GET https://<their-domain>/api/health` → **200** (DB + Redis OK).
- [ ] Create a test account → upload a sample **835** → confirm it ingests, diagnoses, and drafts
      → run one **live** Stripe checkout (test card in test mode first) → confirm
      `subscriptionStatus` activates.

### Step 8 — Onboard the client's real data & users
- [ ] Create the client's **owner** account; invite their billing staff with roles
      (owner / manager / biller).
- [ ] Configure the client's **payer knowledge base** — seed payer-specific appeal rules so
      retrieval + grounded drafting work for *their* payers (the draft gate returns *needs-info*
      until relevant rules exist).
- [ ] Have them upload a real denial batch (or run the **pilot estimator** at `/pilot` for a
      recoverable-$ projection first).

### Step 9 — Monitoring, DR & handover
- [ ] Point an **uptime monitor** at `/api/health`; wire **Sentry** alerts.
- [ ] Walk the client (or your ops) through `docs/runbooks/disaster-recovery.md` (Neon PITR,
      RPO ≤ 5 min / RTO ≤ 60 min) and run one **DR drill**.
- [ ] Hand over: admin guide, support channel/SLA, and the billing relationship.

---

## Selling checklist (TL;DR)

1. Qualify: **Model 1** (SaaS tenant) unless the client demands account ownership → **Model 2**.
2. Sign the **BAA(s)**.
3. Model 1 → invite/provision the tenant, they subscribe, done.
4. Model 2 → collect credentials (Step 2 table), deploy (Steps 3–7), onboard data (Step 8),
   set up monitoring + DR (Step 9).
5. Always: keep PHI out of Stripe/analytics; unique `PHI_ENCRYPTION_KEY` per client; secrets in a
   manager, never in git.
