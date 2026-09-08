# ADR-015 — Solo part-time delivery: four pillars kept, depth cut to a walking skeleton

**Status:** Accepted · 2026-09-08 · **Supersedes ADR-013** · **Resolves ADR-005** ·
Amends the depth (not the breadth) of ADR-004

## Context

The delivery assumptions in the original roadmap were four engineers over eight
months. The actual team is **one part-time developer working under 10 hours per
week**, with some React Native experience, no deadline, no customer waiting, and
no commercial pressure ("build it, then decide").

ADR-004 (all four pillars at launch) has been reaffirmed twice by the product
owner and stands.

## The arithmetic, stated plainly

| Scope | Effort | At 8 hrs/week |
|---|---|---|
| Four pillars at the depth originally specified | 2,700–3,400 hrs | **6–8 years** |
| Four pillars at the depth defined below | ~1,100 hrs | **~2.5–3 years** |
| Walking skeleton through all four pillars | ~330 hrs | **~10 months** |

These are not estimates open to optimism. They are the reason this ADR exists.

## Decision

**Keep all four pillars. Cut the depth of each until the whole thing is
reachable, and sequence the work so something functions end to end as early as
possible.**

Three structural consequences:

### 1. Vertical slices, not horizontal layers

Rather than building each module to completion in turn, the work is cut into
versions that each function end to end. **ADR-016 defines that structure**, and
`/plans` holds it.

The alternative — modules completed one at a time — leaves nothing working end to
end until very near the finish, which on a long part-time project is where
motivation and accumulated context both die.

### 2. Model full, build thin

Tables are created as specified in `03-data-model.md` — including `tasks`,
`assignments`, serials, batches and the rest — even where the v1 UI only handles
the simple case. Adding a column later is trivial; re-shaping a table that
already holds two years of a real company's data is not.

So: one assignee per job in the UI, but the `assignments` table exists. One
branch in the UI, but `branch_id` is on every row.

### 3. Buy everything that can be bought

At this pace, every week spent on undifferentiated infrastructure is a week not
spent on the product. **PowerSync is adopted directly — the spike in ADR-005 is
cancelled**, because a two-week spike that might end in hand-writing a sync
engine is a risk this project cannot absorb. Likewise Supabase for auth, storage
and realtime, and Inngest for background work.

## What is in and out of v1

**Cross-cutting — IN:** multi-tenancy with RLS, email/password auth, four roles
(owner, admin, dispatcher, engineer), audit log, English default + Arabic with
RTL, in-app and push notifications, offline sync, backups.

**Cross-cutting — OUT:** billing (invoice manually), **customer portal
(supersedes ADR-013)**, public API and webhooks, platform admin console (use SQL
and a feature flag), WhatsApp and SMS, MFA, impersonation, branch and team UI,
import tooling, reporting beyond a handful of counts.

| Pillar | In v1 | Deliberately out |
|---|---|---|
| **Forms** | Builder, ~10 field types, conditional visibility, immutable versioning, web + native renderers, submissions, PDF export | Repeatable sections, calculated and reference fields, tables, barcode, scoring and pass/fail, template library, amendments, bulk export |
| **Field tasks** | Customers, sites, assets, jobs, job types, status machine, single assignee, scheduled date, required forms, completion gate | Drag-and-drop dispatch board, multi-assignee, tasks within jobs, skills and certifications, recurring/PM, SLA timers, time entries, costing |
| **Inventory** | Items, warehouse and van locations, append-only movement ledger, transfer to van, consume on job from mobile | Serials, batches, expiry, reservations, purchase orders, suppliers, stock counts, average costing, reorder points, barcode scanning |
| **Meetings** | Meetings, internal attendees, RSVP, minutes, action items with assignee and due date | Recurrence, availability finder, ICS feed, toolbox talks, external attendees, agenda items, calendar sync |

Everything in the right-hand column is specified in the module documents and
stays there. It is deferred, not deleted, and the data model already
accommodates it.

## Web is admin-only

The web app serves administrators and dispatchers on a desktop; the Expo app
serves field engineers. **No engineer-facing web screens are built.** This
removes roughly a third of the web surface at no cost to anyone, since an
engineer with a phone in a plant room was never going to use a browser.

## Consequences

- The two levers on total effort are **hours per week** and **depth**. Breadth is
  fixed by ADR-004 and is not revisited.
- No schedule is published. Work is sequenced, not dated (ADR-016) — under this
  delivery model a schedule is fiction that decays into guilt, while an ordered
  sequence with clear exit criteria stays useful indefinitely.
- Context loss between sessions is the dominant risk on a project at this pace,
  not engineering difficulty. The documentation set is therefore load-bearing:
  every phase ends with its decisions written down.
- No App Store presence, payment gateway, WhatsApp BSP or company registration is
  needed until far later — all carry ongoing maintenance burden and none are on
  the critical path (`16-open-questions.md` Q15).
- Design partners are deferred by product-owner decision
  (`18-design-partner-program.md`). The phase order below is arranged so the
  surfaces that most need real users — the dispatch board above all — are the
  last things built.
