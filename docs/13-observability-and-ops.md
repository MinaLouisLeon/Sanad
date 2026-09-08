# 13 — Operations, Observability & Reliability

## 1. Environments

| Environment | Purpose | Data |
|---|---|---|
| Local | Development | Seeded fixtures |
| Preview | Per pull request | Ephemeral branch database, seeded |
| Staging | Pre-production verification | Anonymized production-shaped data |
| Production | Live | Real |

Staging must be **production-shaped, not production-sized-toy**. Performance
problems and RTL layout failures appear at 50,000 jobs and never at 50. The
anonymization pipeline is part of the build, not a manual script.

## 2. Deployment

- Trunk-based development, short-lived branches, PR required.
- CI on every PR: typecheck → lint → unit → integration → **tenant isolation
  suite** → build → E2E on preview.
- Vercel preview deploy per PR with its own database branch.
- Production deploys are atomic with instant rollback.
- Mobile: EAS Build, OTA updates for JS-only changes, staged rollout.

### Database migrations

**Expand/contract only.** No migration may break the currently running version,
because deploys are not instantaneous and mobile clients lag by days or weeks.

```
1. Expand   add nullable column / new table, deploy
2. Backfill background job, batched, resumable
3. Migrate  write to both, read from new, deploy
4. Contract drop the old column — a separate release, after clients have caught up
```

Rules: migrations are forward-only in production (roll forward, never down);
every migration is reviewed for lock behaviour on large tables; index creation
uses `CONCURRENTLY`; long backfills run in the job runner, not in the migration.

## 3. Observability

**Logging** — structured JSON, always carrying `request_id`, `tenant_id`,
`user_id`, `route`, `duration_ms`. Never log PII, tokens, passwords, or form
values. Retained 30 days hot, 12 months archived.

**Tracing** — OpenTelemetry across API → database → job runner → external
providers. Traces tagged with `tenant_id`, which is what makes "this one customer
says it is slow" a two-minute investigation rather than a two-day one.

**Metrics** — RED (rate, errors, duration) per endpoint; job queue depth, latency
and failure rate; database connections, slow queries, replication lag; sync
success rate and conflict rate per tenant; push and message delivery rates;
storage growth.

**Errors** — Sentry for web, API and mobile, with source maps, release tracking
and tenant tagging. Payloads scrubbed of customer data.

**Business dashboards** — jobs created and completed per day, active tenants,
active mobile devices, forms submitted, sync failures, activation funnel. These
detect a broken deployment faster than infrastructure alerts, because a sudden
drop in completed jobs is the first sign something is wrong.

**Uptime** — external synthetic checks against login, dispatch board load, mobile
sync and API health, from a Gulf region. A public status page.

## 4. Alerting

Alert on symptoms users feel, not on causes.

| Severity | Examples | Response |
|---|---|---|
| **SEV1** page immediately | Site down, sync broken, auth broken, cross-tenant leak, data loss | 15 min |
| **SEV2** page in hours | Error rate > 2%, p95 > 2 s, queue backing up, payment webhooks failing | 1 hour |
| **SEV3** ticket | Elevated errors on one endpoint, single-tenant issue | Next business day |
| **SEV4** review | Slow query trend, storage growth | Weekly |

Every alert links to a runbook. Alerts without runbooks get tuned or deleted —
an alert nobody knows how to action trains the team to ignore alerts.

## 5. Backup and disaster recovery

- Postgres PITR with continuous WAL archiving; **RPO ≤ 5 minutes**.
- Daily full snapshots retained 30 days; weekly retained 12 weeks; monthly
  retained 12 months.
- Object storage versioned with cross-region replication.
- Backups encrypted, and stored in a separate account or project from production
  so that a compromise of production credentials cannot destroy the backups.

**Quarterly restore drill, executed and documented.** An untested backup is not a
backup. The drill measures actual RTO against the 1-hour target and the result is
recorded.

Documented recovery scenarios: accidental tenant deletion (restore that tenant's
rows from PITR into a staging copy, then re-import), region outage (restore into
an alternate region, DNS cutover), corrupt migration (roll forward with a fix),
and ransomware or credential compromise (isolate, rotate everything, restore from
immutable backup).

## 6. Capacity and cost

Cost drivers in order: object storage and egress (photos dominate), database
compute, WhatsApp and SMS, maps API calls, PDF rendering.

Controls: client-side image compression, thumbnails generated once and reused,
lifecycle rules moving old photos to infrequent-access storage, aggressive
caching of geocoding results (addresses do not move), and per-tenant metering so
that a heavy customer's cost is visible against their plan.

**Cost per tenant is tracked as a product metric.** A plan that is unprofitable
at scale is much cheaper to discover in month three than in year two.

## 7. Support operations

- In-app help centre and contextual guidance.
- Ticketing integrated with tenant context, so an agent sees plan, version and
  recent errors without asking.
- Audited impersonation for reproducing issues (`02-tenancy-and-rbac.md` §2).
- Per-tenant health view in the admin console: last sync per device, failed jobs,
  error rate, storage use, seat utilization.
- Support hours aligned to Gulf business hours (Sunday–Thursday), which is a
  calendar most support tooling defaults wrongly.

## 8. Runbooks required before launch

Sync backlog growing; database connection exhaustion; payment provider outage;
WhatsApp template rejection; storage quota exhaustion; a tenant reporting missing
data; suspected cross-tenant exposure; mobile release rollback; restoring a
single tenant; rotating a compromised secret.

Each written, each rehearsed once, each linked from the alert that triggers it.
