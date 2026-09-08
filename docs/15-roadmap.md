# 15 — Delivery Model

**The build plan lives in [`/plans`](../plans/README.md).** This document covers
only *how* delivery is organised and *at what depth* the product ships. The
sequence of work, its phases and its exit criteria are in the plans folder.

## 1. No schedule

There are no dates, durations or estimates in the plan. Work is **sequenced, not
scheduled**. A version is done when its exit criteria are met.

This is deliberate. The delivery model is one part-time developer with no
deadline and no customer waiting (ADR-015). Under those conditions a schedule is
fiction that decays into guilt, while a well-ordered sequence with clear exit
criteria stays useful indefinitely — you can put the project down for a month and
pick it up knowing exactly where you are.

## 2. Versions, each with phases (ADR-016)

Nine versions, each a **vertical slice that works end to end** and is deployed to
production before the next begins.

Production is therefore not a destination at the end of the plan. It is entered
at v0.1, and every version after is an increment on a live system. Migrations,
backups, RLS and deploys are exercised repeatedly rather than invented once,
under pressure, at the end. Each version also leaves the project in a state where
stopping is not a failure.

| Version | What exists when it ships |
|---|---|
| v0.1 · Platform foundation | Multi-tenant, auth, roles, audit log, English/Arabic, deployed |
| v0.2 · Work orders | Customers, sites, assets, jobs, statuses, assignment |
| v0.3 · Forms on jobs | Versioned forms rendered on the web and attached to jobs |
| v0.4 · Mobile field app | Expo with offline sync, photos, signature |
| v0.5 · Form builder + PDF | Admins build forms with no code; branded bilingual PDF |
| v0.6 · Inventory | Movement ledger, warehouse and van stock, consume-on-job |
| v0.7 · Meetings + notifications | Fourth pillar; in-app, push and email delivery |
| v0.8 · Arabic + hardening | Full RTL pass, offline suite, security and performance |
| v1.0 · Production release | Onboarding, verified backups, monitoring, first real tenant |

All four pillars (ADR-004) are present by v0.7. The ladder controls **order**,
never whether something ships.

Inside every version, the same four phases: **Decide → Build → Verify → Release**.

## 3. Depth, not breadth, is what was cut

ADR-004 (all four pillars) stands. ADR-015 reduces the depth of each pillar to
fit the delivery model. The per-pillar in-and-out table in ADR-015 is
authoritative for v1.0; the module documents in `docs/` describe the full target
state, and everything cut is deferred rather than deleted.

Two rules follow from this and are enforced throughout the plans:

- **Model full, build thin.** Tables are created as specified in
  `03-data-model.md` even where the UI only handles the simple case.
- **The unrepeatable things are never deferred.** Tenant isolation, form version
  immutability, the append-only stock ledger, the audit log and English/Arabic
  parity are built into the version that first touches them, because none of them
  can be retrofitted.

## 4. Ordering principle

The surfaces that cannot be designed without watching real users are built
**last**. Design partners are deferred by product-owner decision
(`18-design-partner-program.md`), so the dispatch board, scheduling intelligence
and SLA logic sit after v1.0, by which point there may be someone actually using
the product. The parts that are identical in every field-service business come
first.

## 5. Not needed yet

Off the critical path entirely, contrary to the original funded-team plan:

| | When it becomes real |
|---|---|
| Company registration, trade licence | Shortly before the first paying customer |
| Payment gateway | With billing, after v1.0 |
| WhatsApp BSP | After v1.0 |
| App Store presence | v1.0 — internal distribution is enough until then |
| Trademark search | Before any public launch or brand spend |
| Design partners | Deferred by decision |

## 6. After v1.0

Prioritised from real usage rather than assumption. The backlog is everything
ADR-015 deferred: the dispatch board, recurring and preventive maintenance, SLA
timers, the customer portal, billing, serials and batches, purchase orders, stock
counts, the public API, and the deeper form field types.

`docs/00-product-overview.md` §6 lists what was scoped out and why.
