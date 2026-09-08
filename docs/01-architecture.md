# 01 — System Architecture

## 1. Architectural style

A **modular monolith** with a shared Postgres database, deployed as a Next.js
application on Vercel plus an Expo mobile client, with durable background
workflows handled by a dedicated job runner.

Microservices are rejected for v1. With a small team, four modules launching
together and no proven scaling bottleneck, distributed transactions and
cross-service tenant-context propagation would consume the entire budget. The
module boundaries below are enforced in code (each module owns its tables and
exposes a service interface) so that any module can be extracted later if load
justifies it.

## 2. High-level topology

```
                     ┌──────────────────────────────┐
  Web (browser)  ───▶│  Next.js App Router          │
                     │  - RSC pages + server actions│
  Mobile (Expo)  ───▶│  - Hono API (/api/v1)        │───▶ Postgres (RLS)
                     │  - Auth middleware           │        │
  3rd party API  ───▶│  - Tenant context resolver   │        │
                     └──────────────────────────────┘        │
                              │        │                     │
                              │        └────▶ Object storage (photos, PDFs, files)
                              │
                              ▼
                     ┌──────────────────────────────┐
                     │  Inngest (durable workflows) │───▶ Postgres
                     │  - notifications fan-out     │
                     │  - recurring PM generation   │
                     │  - SLA timers & escalation   │
                     │  - PDF rendering             │
                     │  - stock rollup, reports     │
                     └──────────────────────────────┘
                              │
                              ▼
        Email · SMS · WhatsApp · Push · Payment gateway · Maps
```

Realtime updates (dispatch board, job status) are pushed to connected clients
via Postgres change streams.

## 3. Technology choices

| Concern | Choice | Rationale |
|---|---|---|
| Web framework | **Next.js (App Router) + TypeScript** | Team's existing stack; RSC reduces client payload on data-heavy screens |
| Styling | **Tailwind CSS** with logical properties | RTL-safe by construction when logical properties are used |
| Database | **Postgres** (Supabase or Neon) | RLS is the isolation mechanism; JSONB for form data; mature |
| ORM | **Drizzle** | Thin over SQL, predictable in serverless, does not fight RLS or `SET LOCAL` |
| Auth | **Supabase Auth** + custom access-token hook | Injects `tenant_id`/`role` into the JWT, which RLS policies read directly |
| API layer | **Hono + Zod OpenAPI**, mounted at `/api/v1` | One API serves web, mobile and third parties; OpenAPI generates typed clients |
| Background jobs | **Inngest** | Durable, serverless-native, first-class delayed steps for SLA timers and PM schedules |
| Realtime | **Postgres change streams** (Supabase Realtime) | No extra infrastructure; respects RLS |
| File storage | **Supabase Storage** (S3-compatible) | Presigned direct upload; RLS-integrated policies. Re-evaluate Cloudflare R2 if photo egress cost becomes material |
| Mobile | **Expo (React Native) + expo-router** | Native camera/GPS/barcode/push; shares TypeScript domain packages with web |
| Mobile local DB | **SQLite** (expo-sqlite / op-sqlite) | Real offline queries, not a key-value cache |
| Offline sync | **PowerSync** (evaluate) or custom mutation queue | See §7 and `08-mobile-and-offline.md` |
| Validation | **Zod** | One schema shared by API, forms, and mobile |
| Email | Resend or Amazon SES | Transactional; SES if volume/cost dominates |
| SMS + WhatsApp | **Unifonic** (regional) or Twilio | Unifonic has stronger Gulf carrier routes and local presence |
| Maps / geocoding | **Google Maps Platform** | Materially better MENA address and POI coverage than alternatives |
| Payments | **Tap / HyperPay / Moyasar** (Gulf), **Paymob** (Egypt), Stripe (international) | Behind a provider interface — see `10-billing-and-plans.md` |
| Error tracking | **Sentry** | Team already operates it |
| Tracing / metrics | OpenTelemetry → Axiom or Grafana Cloud | Tenant-tagged traces |
| Search | Postgres full-text v1; Typesense later | Avoid a second datastore until justified |
| PDF rendering | Headless Chromium in a container (Cloud Run / Fly) invoked by Inngest | Vercel function limits make in-process PDF rendering fragile |
| CI/CD | GitHub Actions + Vercel | Preview deploys per PR |
| Monorepo | **Turborepo + pnpm** | Shares domain and form-schema packages across web and mobile |

## 4. Repository layout

```
sanad/
├── apps/
│   ├── web/                  Next.js — tenant application
│   │   └── src/app/[locale]/(app|auth|public)/...
│   ├── admin/                Platform admin console (separate deployment + domain)
│   └── mobile/               Expo app for field engineers
├── packages/
│   ├── db/                   Drizzle schema, migrations, RLS policies, seeds
│   ├── core/                 Domain logic per module (pure, framework-free)
│   │   ├── tenancy/  identity/  customers/  assets/
│   │   ├── field-ops/  forms/  inventory/  meetings/
│   │   ├── notifications/  billing/  audit/
│   ├── forms-engine/         Form schema types, validation, conditional-logic evaluator
│   ├── forms-renderer-web/   React renderer
│   ├── forms-renderer-native/React Native renderer
│   ├── api-contract/         Zod + OpenAPI definitions, generated clients
│   ├── ui/                   Shared design system (web)
│   ├── ui-native/            Shared design system (mobile)
│   ├── i18n/                 Message catalogs, formatters, calendar utilities
│   └── config/               ESLint, TS, Tailwind presets
├── services/
│   └── pdf-renderer/         Containerized Chromium PDF service
├── infra/                    IaC, migration runner, backup scripts
└── docs/                     This specification set
```

**Rule:** `packages/core/*` never imports Next.js, React, or Expo. Domain logic
must be testable in isolation and reusable by both clients and the job runner.

## 5. Request lifecycle and tenant context

Every request resolves tenant context exactly once, at the edge of the system:

1. Verify the JWT; extract `user_id`, `tenant_id`, `branch_ids`, `role`.
2. Open a database transaction and issue
   `SET LOCAL app.tenant_id = '<uuid>'` and `SET LOCAL app.user_id = '<uuid>'`.
3. RLS policies on every tenant-scoped table filter on `app.tenant_id`.
4. Domain services run inside that transaction and never accept a `tenantId`
   parameter from the caller — it comes from context only.
5. Audit entries are written in the same transaction as the change.

Consequences: a developer who forgets a tenant filter gets zero rows, not
another company's data. A compromised client cannot escalate by tampering with a
request body, because tenant identity comes only from the signed token.

Background jobs must set the same context explicitly; a job without tenant
context is a bug and fails loudly rather than reading across tenants.

## 6. Module boundaries

| Module | Owns | May depend on |
|---|---|---|
| tenancy | tenants, branches, plans, feature flags | — |
| identity | users, memberships, roles, sessions, invitations | tenancy |
| customers | customers, sites, contacts, contracts | tenancy |
| assets | assets, asset types, service history | customers |
| field-ops | jobs, tasks, assignments, schedules, SLA | customers, assets, identity |
| forms | templates, versions, submissions, values | identity |
| inventory | items, locations, movements, serials, counts | field-ops (consumption) |
| meetings | meetings, attendees, agendas, minutes, actions | identity |
| notifications | templates, preferences, deliveries | all (event consumer) |
| billing | subscriptions, seats, usage, invoices | tenancy |
| audit | audit log, retention | all (event consumer) |

Cross-module communication goes through published service interfaces or domain
events, never direct table access. `notifications` and `audit` are pure
consumers — nothing depends on them, so they can never create a cycle.

## 7. The offline sync decision

The riskiest technical component. Two viable paths:

**Option A — PowerSync (recommended to evaluate first).** A managed sync layer
between Postgres and mobile SQLite. Handles bidirectional streaming, initial
hydration, reconnection, and partial replication by sync rules. Saves an
estimated 6–10 engineering weeks and removes an entire class of bugs. Costs:
a per-connection vendor fee, sync rules must be written to respect tenant
scoping, and it constrains some schema choices.

**Option B — Custom mutation queue.** Local SQLite mirror, an append-only
outbox of mutations with client-generated UUIDs, replayed on reconnect with
idempotency keys; server returns authoritative state. Full control, no vendor,
but reconnection, partial-failure and ordering edge cases are where field apps
die.

**Recommendation:** timebox a two-week spike on PowerSync during Milestone 0
against a realistic slice (a day of jobs with photos and a form submission). If
it holds, adopt it; if not, fall back to Option B with the spike's learnings.
Either way, entity IDs are **client-generated UUIDv7** from day one, so an
engineer can create a job or submission offline without a server round trip.

## 8. Data residency constraint (important)

Neither Vercel nor the managed Postgres providers currently offer a
Saudi or UAE region. Gulf government entities and some regulated industries
require in-country data storage.

**Plan:** ship the multi-tenant cloud in `eu-central-1` (Frankfurt) — the lowest
latency option with mature availability. Keep the application fully
containerizable so that a **single-tenant deployment** (AWS `me-south-1` Bahrain
or `me-central-1` UAE) can be sold as an enterprise tier without a rewrite. This
means: no Vercel-only primitives in domain code, all configuration via
environment variables, and a Docker build maintained in CI from day one even
though the cloud product does not use it.

Latency to Gulf users from Frankfurt is roughly 90–130 ms — acceptable for the
web app; the mobile app's offline-first design makes it irrelevant for engineers.

## 9. Non-functional targets

| Metric | Target |
|---|---|
| Availability | 99.9% monthly (≈ 43 min downtime) |
| p95 API latency | < 300 ms |
| p99 API latency | < 800 ms |
| Dispatch board interactive | < 1.5 s on a warm cache |
| Mobile sync of a day's work | < 10 s on 3G |
| Photo upload | Background, resumable, survives app termination |
| RPO | ≤ 5 minutes |
| RTO | ≤ 1 hour |
| Max tenant size (v1) | 500 users, 5k jobs/month, 50k submissions/year |

## 10. Scaling path (when, not now)

1. Read replicas for reporting and dashboards.
2. Move form-submission values to partitioned tables by month.
3. Extract PDF rendering and reporting to dedicated services (already separate).
4. Promote large tenants to dedicated databases (the tenant router is designed
   for this — connection resolution is by tenant, not global).
5. Introduce a search cluster when Postgres full-text degrades.

None of this is built now. Each is unblocked by decisions taken now.
