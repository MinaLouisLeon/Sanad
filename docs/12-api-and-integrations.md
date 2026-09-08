# 12 — Public API & Integrations

## 1. Why this is in v1, not later

Every enterprise deal in this category asks two questions: "does it integrate
with our accounting system" and "can we get our data out". A product without an
API loses those deals regardless of feature depth. Building the internal API as
the public API from the start costs little; retrofitting a public surface onto
internal endpoints costs a rewrite.

**One API serves all three consumers** — web app, mobile app, third parties —
built with Hono + Zod OpenAPI at `/api/v1`. Differences are in authentication and
rate limits, not in a parallel implementation.

## 2. Design

- REST, resource-oriented, JSON.
- Versioned in the path (`/api/v1`). Breaking changes create `/v2`; the previous
  version is supported for at least 12 months after deprecation notice.
- Cursor pagination (never offset — it breaks on concurrent inserts).
- Consistent envelope: `{ data, meta: { cursor, has_more } }`.
- Errors: RFC 9457 problem details with a stable machine-readable `code`, a
  human message, and field-level detail on validation failures.
- Idempotency: `Idempotency-Key` header honoured on all POST endpoints, which is
  what makes safe retries possible for both the mobile app and third parties.
- Filtering, sorting and sparse fieldsets on collections.
- `Retry-After` on 429; rate-limit headers on every response.
- **OpenAPI spec generated from the Zod definitions**, so it cannot drift from
  the implementation. Typed clients are generated for web and mobile from the
  same source.

## 3. Authentication

| Consumer | Mechanism |
|---|---|
| Web app | Session cookie → short-lived JWT |
| Mobile app | JWT + rotating refresh token |
| Third-party server | API key (`sk_live_…`), tenant-scoped, hashed at rest |
| Third-party user-context | OAuth 2.0 authorization code + PKCE (Phase 3) |

API keys carry a **scoped permission set** — a key for an accounting integration
gets `customers:read` and `invoices:write`, nothing more. Keys are listed with
last-used timestamps, individually revocable, and rotatable without downtime.
Key usage is rate-limited and audited separately from user activity.

## 4. Resources exposed (v1)

Read and write: customers, sites, contacts, assets, jobs, tasks, assignments,
form templates and versions (read), form submissions (read + create), items,
stock levels, stock movements, users (read), meetings.

Read-only: audit log, reporting aggregates, plan entitlements.

Deliberately not exposed: billing internals, platform administration, other
tenants' anything.

## 5. Webhooks

Push beats poll, and integrators expect it.

- Tenant-configurable endpoints with event subscriptions.
- Events mirror the notification catalogue: `job.created`, `job.status_changed`,
  `job.completed`, `form.submitted`, `form.verified`, `stock.movement_created`,
  `stock.below_reorder`, `asset.created`, `meeting.completed`, and others.
- **Signed** with HMAC-SHA256 over the raw body plus a timestamp, in a
  `Sanad-Signature` header, with a documented verification recipe and a
  replay-protection window.
- At-least-once delivery with exponential backoff over roughly 24 hours;
  consumers must be idempotent and the docs say so plainly.
- Delivery log visible in-app with request/response bodies and a manual replay
  button — this single screen removes most integration support tickets.
- Auto-disable after sustained failure, with notification to the tenant admin.

## 6. Integrations to build or enable

**Phase 3, ranked by regional demand:**

| Integration | Rationale |
|---|---|
| **Zoho Books / Zoho CRM** | Dominant in Gulf SME; ZATCA-compliant; the most requested integration in this segment by a wide margin |
| **QuickBooks / Xero** | International customers and larger SMEs |
| **Microsoft 365** (Calendar, Teams, Entra ID) | Standard enterprise stack in the region |
| **Google Workspace** | Calendar, Drive |
| **WhatsApp Business** | Already core to notifications; inbound handling extends it |
| **Odoo** | Common ERP in regional contracting |
| **Zapier / Make** | Long-tail integrations without engineering effort |
| **Power BI / Looker Studio** | Executive reporting; needs a read-only analytics connector |
| **Fleet telematics** | Vehicle location for dispatch; relevant to larger FM contractors |
| **SAP / Oracle** | Enterprise only, project-based, not a productized connector |

Zoho is worth emphasizing: it is disproportionately dominant in Gulf SME
back-office software compared to its global share, and an accounting integration
is frequently a procurement checklist item.

## 7. Import and export

**Import (essential for onboarding — a customer will not retype 4,000 assets):**
- Guided CSV/Excel import for customers, sites, contacts, assets, items, opening
  stock balances and users.
- Column mapping UI with saved mappings, validation preview showing errors before
  commit, dry-run mode, and a rollback window on the import batch.
- Import is a first-week sales blocker, not a nice-to-have. A tenant that cannot
  load its asset register does not start the trial.

**Export:**
- Any list view exports to CSV/Excel respecting current filters.
- Scheduled report exports delivered by email.
- Full tenant export (JSON + files + form PDFs) available self-service, which is
  both a GDPR obligation and a trust signal in sales conversations.

## 8. Developer experience

Public documentation site with an OpenAPI reference, authentication guide,
webhook verification examples in several languages, a sandbox tenant with seeded
data, and a Postman collection. A changelog with deprecation notices, and a
stated support policy for each API version.
