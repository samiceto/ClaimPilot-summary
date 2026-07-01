# Disaster Recovery Runbook (T10.5)

_Last updated: 2026-06-26_

Targets and procedure for recovering ClaimPilot after data loss or a regional
outage. PHI is stored encrypted (AES-256-GCM, app layer); recovery must preserve
the encryption key separately from the database.

## Objectives

| Metric | Target | Rationale |
|---|---|---|
| **RPO** (max data loss) | ≤ 5 min | Neon retains continuous WAL; point-in-time restore is granular to the second. |
| **RTO** (max downtime) | ≤ 60 min | Branch-restore + app redeploy. |

## Components & sources of truth

| Component | Store | Backup mechanism |
|---|---|---|
| Relational data (claims, denials, appeals, outcomes, audit log) | Neon Postgres | Continuous WAL / point-in-time restore (PITR) |
| PHI documents (835/EOB) | object storage (`PHI_STORAGE_*`) | bucket versioning + lifecycle |
| PHI encryption key | `PHI_ENCRYPTION_KEY` (secret manager) | **must be backed up out-of-band; DB restore is useless without it** |
| Queues (BullMQ) | Redis | ephemeral — jobs are idempotent and replayable from DB state |

## Restore procedure (Neon PITR)

1. **Declare** the incident; capture the target restore timestamp `T` (just before corruption/loss).
2. In Neon, **create a branch** from the timestamp `T` (`Branches → Restore → point in time`). Do **not** overwrite production until verified.
3. Point a **staging** deployment at the restored branch (`DATABASE_URL` → branch pooled URL, `DATABASE_URL_UNPOOLED` → branch direct URL).
4. **Verify** on staging (see checklist). Confirm `PHI_ENCRYPTION_KEY` decrypts restored PHI.
5. **Cut over**: promote the restored branch to primary (or repoint prod `DATABASE_URL` to it), redeploy `apps/web` + `apps/worker`.
6. **Replay**: workers re-enqueue pending jobs from DB state (denials without appeals, appeals without outcomes). No queue restore needed.
7. **Close**: record actual RPO/RTO; file a post-incident review.

## Verification checklist (post-restore)

- [ ] `GET /api/health` returns `200 {status:"ok"}` (DB + Redis reachable).
- [ ] Row counts for `claims/denials/appeals/outcomes` within expected range for `T`.
- [ ] `verifyAuditChain()` passes on a tenant's audit events (tamper-evident chain intact).
- [ ] A known encrypted PHI field decrypts correctly (validates the key matches the restored data).
- [ ] Latest `drizzle` migration present (`select * from __drizzle_migrations order by id desc limit 1`).
- [ ] Worker boots and registers all 3 BullMQ queues; a test denial flows ingest→classify.

## DR drill (run quarterly — **manual / owner-executed**)

> The drill is an operational exercise against live infra; it is intentionally not
> automated. Execute and record results here each quarter.

1. Restore a Neon branch to a timestamp ~1h ago.
2. Run the full verification checklist against it.
3. Measure wall-clock **RTO** (declare → checklist green) and the restore-point gap (**RPO**).
4. Tear down the drill branch.

| Drill date | RPO observed | RTO observed | Pass? | Notes |
|---|---|---|---|---|
| _pending first drill_ | — | — | — | — |

## Alerting (T10.5)

| Signal | Source | Threshold → action |
|---|---|---|
| App/worker errors | Sentry (`NEXT_PUBLIC_SENTRY_DSN`, `SENTRY_*`) | error rate spike → page on-call |
| Uptime | `GET /api/health` via external monitor | non-200 for 2 min → page on-call |
| Missed appeal deadline | worker deadline-check (T10.2 never-miss) | any appeal within threshold unalerted → alert owner |
| Recovered-$ / win-rate trend | `/api/analytics`, PostHog (`NEXT_PUBLIC_POSTHOG_*`) | weekly business review (not paging) |

_Status: health endpoint + this runbook are in place. Wiring the external Sentry
alert rules, the uptime monitor, and executing the first DR drill are
owner/infra tasks (need console access + a maintenance window)._
