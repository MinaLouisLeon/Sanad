# 15 — Delivery Roadmap

## 1. The constraint

The scope decision (ADR-004) is **all four pillars at launch**: forms, field ops,
inventory and meetings, with an offline mobile app, Arabic-first UI, and regional
billing.

This is a large first release. The plan below manages that risk in two ways:
every milestone ends in **working, demonstrable software** rather than a
half-built layer, and a **pilot tenant is using the product in production from
month five**, well before general availability. The alternative — integrating
four modules for the first time in month seven — is the failure mode that sinks
releases of this size.

## 2. Team shape

| Role | Allocation | Focus |
|---|---|---|
| Tech lead / backend | 1.0 | Architecture, tenancy, RLS, API, code review |
| Full-stack engineer | 2.0 | Web app, forms builder, dispatch, inventory |
| Mobile engineer | 1.0 | Expo app, sync engine, offline |
| Product designer | 0.5 | Dispatch board, mobile flows, Arabic/RTL system |
| QA engineer | 0.5 | Isolation suite, offline scenarios, E2E, device matrix |
| DevOps | 0.25 | CI/CD, environments, backups, monitoring |

**Minimum viable team is 4 engineers.** Below that, the timeline extends
proportionally rather than compressing — the offline sync engine and the forms
engine cannot be meaningfully parallelized further.

## 3. Milestones

Two parallel tracks after M0. Weeks are elapsed weeks from project start.

### M0 — Foundations (weeks 1–5)

*Nothing user-visible; everything downstream depends on it.*

- Monorepo, CI/CD, environments, preview deploys
- Postgres schema core, Drizzle setup, migration tooling
- **Tenancy + RLS + the isolation test suite** (blocking gate for all later work)
- Auth: email/password, phone OTP, sessions, MFA scaffolding
- RBAC: roles, permissions, scopes
- Audit log
- i18n foundation: Arabic-first, RTL layout system, typography, formatters
- Design system: web and native primitives
- Observability: Sentry, structured logging, tracing
- **Spike: PowerSync vs custom sync (2 weeks, decision by end of M0)** — ADR-005

*Exit criteria:* two tenants exist, cannot see each other, isolation suite green
in CI, an Arabic RTL page renders correctly, a signed-in user reaches an empty
dashboard on web and mobile.

---

### Track A — Web platform

#### M1 — Core records & forms builder (weeks 6–12)
- Customers, sites, contacts, contracts
- **Assets and asset types**, QR generation, service history view
- Form builder: canvas, all v1 field types, conditional logic editor
- Form versioning, publish flow, diff summary, validation
- Web form renderer, submission viewer
- Platform template library (8 starter forms)
- CSV import for customers, sites, assets

*Exit:* an admin builds a real inspection form and submits it on the web.

#### M2 — Field operations (weeks 13–18)
- Job types, jobs, tasks, assignments; the state machine
- **Dispatch board**: timeline, drag-drop, conflict detection, filters, RTL
- Scheduling: availability, skills, certification checks, proximity ranking
- Recurring/PM generation with holiday and weekend awareness
- SLA policies, timers, escalation
- Time entries, job costing
- Job detail screen with forms, photos, history

*Exit:* a dispatcher schedules a full week for ten engineers, with SLA tracking.

#### M4 — Inventory (weeks 19–23)
- Items, categories, locations including vans
- Stock movement ledger, rollup, reservations
- Serials, batches, expiry
- Transfers, van replenishment, stock counts with variance approval
- Suppliers, purchase orders, receiving
- Costing, stock reports

*Exit:* stock moves warehouse → van → job, and the ledger reconciles under a
property test.

#### M5 — Meetings & notifications (weeks 24–27)
- Meetings, attendees, RSVP, recurrence, availability finder
- Agenda, minutes, action items, conversion to jobs
- Toolbox talks with signature register
- ICS feed
- Notification service: all channels, preferences, quiet hours, templates
- **WhatsApp templates submitted to Meta for approval — must start by week 22**

*Exit:* a meeting produces an action item that becomes a scheduled job, and
attendees are notified on their chosen channels in their own language.

---

### Track B — Mobile

#### M3 — Mobile app & offline sync (weeks 6–18, running alongside A)
- Weeks 6–9: app shell, auth, navigation, design system, local SQLite schema
- Weeks 10–13: **sync engine** — down-sync, mutation queue, attachments,
  idempotency, conflict inbox
- Weeks 14–16: My Day, job detail, status flow, native form renderer, photos,
  signature, time capture
- Weeks 17–18: van stock, scanning, meetings, offline hardening

*Exit:* the airplane-mode scenario suite passes — a full offline day syncs
cleanly.

---

### M6 — Commercialization (weeks 24–29, alongside M5)
- Billing: plans, seats, subscriptions, trials, dunning
- Payment gateway adapters (Gulf + Egypt), bank-transfer invoicing
- VAT handling; e-invoicing provider integration
- **Platform admin console**: tenants, plans, flags, impersonation, health
- Signup, onboarding wizard, demo data
- Reporting and dashboards across all modules
- Public API v1 documentation, API keys, webhooks

### M7 — Hardening & pilot (weeks 30–35)
- **Pilot with 2–3 real tenants** (begins week 26, in parallel — not after)
- Performance and load testing, index tuning
- Third-party penetration test and remediation
- Backup and restore drill
- Runbooks, status page, support tooling
- Accessibility audit; full Arabic review by a native speaker
- Documentation, help centre, training material

**General availability: end of week 35 (≈ 8 months).**

## 4. Realistic expectations

| Scenario | Team | GA |
|---|---|---|
| Optimistic | 5 engineers, no scope change, pilot goes smoothly | 7 months |
| **Planned** | **4 engineers** | **8 months** |
| Realistic with normal friction | 4 engineers, some rework | 9–10 months |
| Under-resourced | 2–3 engineers | 14+ months |

The largest schedule risks are the sync engine (M3) and the dispatch board (M2).
Both are hard to estimate and both are load-bearing.

## 5. Cut lines if the schedule slips

Agree these **now**, in order, so the decision is not made under pressure:

1. Purchase orders and receiving → post-GA (manual stock receipts remain)
2. Meetings reduced to scheduling + minutes; action items and toolbox talks
   follow one month later
3. Stock counts move to a fast-follow release
4. Serial and batch tracking deferred (quantity tracking ships)
5. Reporting reduced to a fixed dashboard set; custom reports post-GA
6. Public API ships read-only first, writes a month later

**Never cut:** tenant isolation, offline sync correctness, form version
immutability, the audit log, backups, or Arabic RTL quality. Each is either
unrecoverable later or the reason the product exists.

## 6. Post-GA sequence

**Phase 3 (months 9–14)** — customer portal; quotes and invoicing; two-way
Google/Microsoft calendar sync; Zoho and QuickBooks integrations; inbound
WhatsApp; suggested scheduling; partner/reseller accounts.

**Phase 4 (months 15+)** — SSO/SAML; white-labelling; custom roles; route
optimization; AI features (voice-to-form dictation first — the highest-value
single feature for field engineers, and a genuine differentiator); predictive
maintenance from asset history; French and Turkish locales; in-country
single-tenant deployments.

## 7. Pre-development checklist

Before the first line of application code:

- [ ] Answers to the blocking questions in `16-open-questions.md`
- [ ] Two or three design-partner tenants committed to the pilot
- [ ] Hosting, database and storage accounts provisioned
- [ ] Payment gateway and WhatsApp BSP applications **started** — both have
      multi-week onboarding and are common launch blockers
- [ ] Domain, DNS and email sending domain configured
- [ ] Repository, CI, environments and branch protection in place
- [ ] Design direction agreed for the dispatch board and mobile job flow
- [ ] Arabic terminology glossary agreed with a native speaker — deciding the
      Arabic word for "job", "asset" and "dispatch" after 200 screens exist is a
      painful retrofit
