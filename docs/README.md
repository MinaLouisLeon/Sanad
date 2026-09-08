# Sanad — Field Service Management SaaS

**Status:** Specification phase. No application code exists yet, by design.
**Branch:** `claude/saas-platform-architecture-u2ippe`

Sanad is a multi-tenant SaaS platform for companies that manage field operations:
work orders and dispatch, an offline-capable mobile app for field engineers,
admin-authored dynamic forms, inventory with van stock, and meeting scheduling.

Primary market is MENA / Gulf, Arabic-first with full RTL support.

---

## How to read these documents

Read in order if you are new to the project. Each document is self-contained
enough to be handed to one person or one team.

| # | Document | Purpose |
|---|----------|---------|
| 00 | [Product Overview](./00-product-overview.md) | Vision, market, personas, scope boundaries |
| 01 | [System Architecture](./01-architecture.md) | Stack, infrastructure, repository layout |
| 02 | [Tenancy, Identity & RBAC](./02-tenancy-and-rbac.md) | Multi-tenancy, isolation, roles, permissions |
| 03 | [Data Model](./03-data-model.md) | Entities and relationships across all modules |
| 04 | [Dynamic Forms](./04-module-forms.md) | Form builder, versioning, submissions, PDF |
| 05 | [Field Operations](./05-module-field-ops.md) | Jobs, dispatch, scheduling, SLA |
| 06 | [Inventory](./06-module-inventory.md) | Stock ledger, van stock, serials, procurement |
| 07 | [Meetings & Calendar](./07-module-meetings.md) | Meetings, availability, action items |
| 08 | [Mobile & Offline](./08-mobile-and-offline.md) | Expo app, sync engine, conflict handling |
| 09 | [Security & Compliance](./09-security-and-compliance.md) | Threat model, audit, GDPR, data residency |
| 10 | [Billing & Monetization](./10-billing-and-plans.md) | Pricing, gateways, VAT, e-invoicing |
| 11 | [Notifications & Localization](./11-notifications-and-i18n.md) | Channels, templates, Arabic/RTL, calendars |
| 12 | [Public API & Integrations](./12-api-and-integrations.md) | REST API, webhooks, third-party integrations |
| 13 | [Operations & Observability](./13-observability-and-ops.md) | Logging, monitoring, backup, DR, deploys |
| 14 | [Testing & Quality](./14-testing-and-quality.md) | Test strategy, tenant isolation proofs |
| 15 | [Delivery Roadmap](./15-roadmap.md) | Milestones, team shape, estimates |
| 16 | [Open Questions](./16-open-questions.md) | Unresolved decisions blocking build |
| — | [ADRs](./adr/) | Architecture decision records |

---

## Non-negotiable design rules

These five rules are load-bearing. Violating any one of them causes damage that
cannot be repaired by a later migration.

1. **Tenant isolation is enforced in the database** via Row-Level Security, never
   by application `WHERE` clauses alone.
2. **Published form versions are immutable.** Editing a form creates a new
   version; submissions always reference the version they were captured against.
3. **Stock is an append-only movement ledger.** Quantity on hand is derived,
   never a mutable column.
4. **The mobile app is offline-first from the first commit.** Offline sync cannot
   be retrofitted onto an online-first data layer.
5. **Every state transition is audited** — who, what, when, from where, old value
   and new value, in an append-only log.
