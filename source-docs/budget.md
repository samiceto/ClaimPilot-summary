# ClaimPilot — Running-Cost Analysis (Infrastructure Budget)

_Prepared 2026-06-27. All figures are **estimates in USD** based on the project's actual stack
and public vendor list prices at time of writing. Vendor pricing changes frequently — treat
these as planning numbers, confirm current quotes before contracting. **The single biggest cost
driver is HIPAA: a signed BAA (Business Associate Agreement) is usually gated behind each
vendor's higher tiers.**_

---

## 1. The stack that costs money

| Component | Vendor (as built) | Billing model |
|---|---|---|
| Postgres + pgvector | **Neon** | tier + compute/storage |
| Web hosting | **Vercel** or **Azure Container Apps** | per-seat / per-vCPU-hour |
| Worker hosting | container host (ACA / Render / Fly / ECS) | per-vCPU-hour (always-on) |
| Redis (queues) | Upstash / Azure Cache / ElastiCache | requests or instance-hour |
| Auth | **Clerk** | MAU-based |
| Billing | **Stripe** | % per transaction (no fixed fee) |
| LLM | **OpenAI / Azure OpenAI** | per-token usage |
| PHI object storage | S3 / Cloudflare R2 / Azure Blob | per-GB + egress |
| Error monitoring | **Sentry** | events |
| Product analytics | **PostHog** | events |
| Domain + email | registrar / Postmark etc. | flat |

---

## 2. ⚠️ The HIPAA BAA multiplier (read this first)

ClaimPilot processes PHI, so **every vendor that can see PHI must sign a BAA**. Most SaaS vendors
only offer a BAA on an enterprise/business plan, which is dramatically more expensive than the
hobby/pro tier you'd use for a non-PHI app:

| Vendor | BAA available on | Practical floor with BAA |
|---|---|---|
| **Neon** | Business plan | ~**$700/mo** |
| **Clerk** | Enterprise | **custom** (typically $$$/mo) |
| **Vercel** | Enterprise only | **custom** (often $20k+/yr) — _this is why the worker + web are better on a cloud you already have a BAA with_ |
| **Azure** (ACA, Cache, Blob, OpenAI) | Microsoft BAA covers all Azure services | included once BAA signed |
| **OpenAI** | Enterprise / API BAA on request | usage price unchanged once signed |
| **AWS / Cloudflare R2** | BAA available | usage price unchanged |
| **Stripe** | **N/A** — never send Stripe PHI | — |
| **Sentry / PostHog** | scrub PHI; BAA on business tiers | keep PHI out instead |

> **Strategic implication:** the cheapest *compliant* path is to **consolidate on one BAA-covered
> cloud** (e.g. **Azure**: Container Apps for web+worker, Azure Cache for Redis, Blob for PHI,
> Azure OpenAI — all under a single Microsoft BAA) rather than stitch together many SaaS vendors
> that each demand their own enterprise BAA. The project already uses Azure OpenAI, so Azure is
> the natural anchor.

---

## 3. AI cost per denial (the usage-variable cost)

Each denial flows through up to four model calls: **extraction** (parse document fields),
**classification** (judgment fields), **embedding** (for retrieval), and **appeal drafting**
(synthesis). Rough token + cost estimate per denial:

| Step | ~Tokens | Cost @ small/fast model | Cost @ frontier model |
|---|---|---|---|
| Extraction | 2–5k in | ~$0.001 | ~$0.02 |
| Classification | 1–2k in | ~$0.0005 | ~$0.01 |
| Embedding | <1k | ~$0.00002 | ~$0.00002 |
| Appeal draft | 3–6k in/out | ~$0.003 | ~$0.05 |
| **Per denial** | | **~$0.005–0.01** | **~$0.08–0.10** |

- **1,000 denials/mo** ≈ **$5–10** (small models) to **$80–100** (frontier models).
- **10,000 denials/mo** ≈ **$50–100** to **$800–1,000**.

Models are configurable (`CLAIMPILOT_EXTRACTION/CLASSIFICATION/SYNTHESIS_MODEL`), so cost scales
with the quality tier you choose. Recommendation: small/fast models for extraction +
classification (grounded by the deterministic CARC reference anyway), a stronger model only for
appeal drafting.

---

## 4. Three running-cost scenarios

### Scenario A — Dev / staging / demo (no BAA, synthetic data only)
For development and sales demos with **no real PHI**, you can use free/hobby tiers.

| Item | Est. /mo |
|---|---|
| Neon (Free/Launch) | $0–19 |
| Vercel (Hobby/Pro) | $0–20 |
| Worker host (small) | $5–15 |
| Redis (Upstash free/pay-go) | $0–10 |
| Clerk (free ≤10k MAU) | $0 |
| AI usage (light testing) | $5–20 |
| Sentry / PostHog (free) | $0 |
| Storage + domain | ~$2 |
| **Total** | **~$15–110/mo** |

### Scenario B — Small production, HIPAA-compliant (1–10 practices)
Real PHI → BAAs required. Consolidated on Azure.

| Item | Est. /mo |
|---|---|
| Neon Business (BAA, PITR) | ~$700 |
| Azure Container Apps (web + worker, modest) | ~$50–150 |
| Azure Cache for Redis (Standard) | ~$55 |
| Azure Blob (PHI, <50 GB) | ~$5 |
| Azure OpenAI (≤5k denials) | ~$30–500 |
| Clerk (Enterprise/BAA) | ~$100–500+ |
| Sentry Team + PostHog | ~$50 |
| Domain + transactional email | ~$15 |
| **Total** | **~$1,050–2,000/mo** |

> Plus **Stripe fees: 2.9% + $0.30 per successful charge** (revenue-linked, not fixed).

### Scenario C — Scaled production (50+ practices, higher volume)
| Item | Est. /mo |
|---|---|
| Neon (Business, scaled compute) | ~$700–1,500 |
| Azure compute (autoscaled web + worker) | ~$300–800 |
| Redis (Standard/Premium) | ~$150–400 |
| Blob storage (more PHI + backups) | ~$20–60 |
| Azure OpenAI (10k–50k denials) | ~$500–3,000 |
| Clerk Enterprise (more MAU) | ~$500–1,500 |
| Sentry + PostHog (business) | ~$150–400 |
| Misc (email, domain, CDN) | ~$50 |
| **Total** | **~$2,400–8,000+/mo** |

---

## 5. One-time / setup costs (not monthly)

- HIPAA risk assessment / compliance review (if outsourced): **$2k–15k**.
- Legal review of BAAs & contracts: **$1k–5k**.
- Build the deploy artifacts (Dockerfiles for web + worker, CI deploy) — **engineering time**,
  currently **not yet in the repo** (see `remaining.md` → PART C2).
- Domain + SSL: ~**$12–50/yr** (SSL usually free via host).

---

## 6. Unit economics (sanity check)

At list pricing — **Practice tier $449/mo** — a single paying practice covers a large share of
Scenario B's fixed infrastructure. Break-even on the HIPAA fixed cost (~$1,050–2,000/mo) is
roughly **3–5 Practice-tier customers** or **1–2 Group-tier ($1,200) customers**. Because the
heaviest variable cost (AI) is only **~$0.01–0.10 per denial**, gross margin per customer is high
once the BAA fixed costs are covered — the model scales well past break-even.

---

## 7. Cost-control levers

1. **Consolidate on one BAA cloud** (Azure) to avoid multiple enterprise BAAs.
2. **Right-size models** — small models for extraction/classification, frontier only for drafting.
3. **Cache embeddings** for the payer knowledge base (already vector-stored; embed once).
4. **Scale the worker to zero / low** during off-hours (queue-driven; Azure Container Apps can
   scale on queue length).
5. **Batch ingestion** to amortize cold starts.
6. **Keep PHI out of Sentry/PostHog** — avoids needing (pricier) BAA tiers there.
