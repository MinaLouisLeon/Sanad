# 15 — Delivery Roadmap

**Delivery model:** one part-time developer, under 10 hours per week, with Claude
assistance. No deadline, no customer waiting. See ADR-015 for how this reshapes
the plan and why.

**Scope:** all four pillars (ADR-004, reaffirmed), at the reduced depth defined
in ADR-015.

## 1. The numbers, honestly

| | Effort | At 8 hrs/wk | At 15 hrs/wk |
|---|---|---|---|
| Walking skeleton — all four pillars, minimal | ~330 hrs | ~10 months | ~5 months |
| Complete thin v1 | ~1,100 hrs | **~2.5–3 years** | ~18 months |

Working software exists from month 10. It gets meaningfully better every three to
four months after that. Nothing here is a guess dressed up as a plan — if the
total is unacceptable, the levers are **hours per week** and **depth**, never
optimism.

Claude makes writing code much faster. It does not shorten debugging offline sync
against a real phone, Arabic RTL review across every screen, or the decisions
only you can make. On a project at this pace those are most of the calendar.

## 2. Sequencing principle

Build the **thinnest complete path through all four pillars first**, then deepen
one pillar at a time.

```
Phase A  ▓▓▓▓  everything works, badly            ← month 10
Phase B  ▓▓▓▓▓ forms become genuinely good        ← month 16
Phase C  ▓▓▓▓  field ops becomes real             ← month 22
Phase D  ▓▓▓   inventory and meetings deepen      ← month 28
Phase E  ▓▓▓   Arabic, hardening, ship            ← month 32
```

The alternative — finishing each module before starting the next — leaves nothing
working end to end until roughly month 22. On a multi-year part-time project,
that is where motivation and accumulated context both die. This ordering trades
polish for a working system, deliberately.

**Ordering rule:** the surfaces that cannot be designed without watching real
users come **last**. Since design partners are deferred by decision, the dispatch
board, scheduling intelligence and SLA logic sit in Phase C and later, by which
point you may have someone using the product.

---

## Phase A — Walking skeleton (~330 hrs · to month 10)

Everything below is the minimum that makes the next thing possible. Resist
polish; Phase B exists for that.

### A1 · Foundations (~110 hrs)
- Turborepo, TypeScript, CI, Vercel deploy, Supabase project
- Postgres schema for the **full** data model (ADR-015: model full, build thin)
- **Tenancy + RLS + the isolation test suite** — never skipped, never deferred
- Auth: email and password only
- Four roles: owner, admin, dispatcher, engineer
- Audit log
- i18n: English catalog, Arabic catalog, RTL layout system, `en`/`ar` parity check
- Minimal design system

*Done when:* two tenants exist, cannot see each other, isolation suite green, and
a page renders correctly in Arabic RTL.

### A2 · Core records, web (~50 hrs)
Customers, sites, assets — plain CRUD, no cleverness.

### A3 · Forms, minimum viable (~70 hrs)
- Form schema types and validation in `packages/forms-engine`
- **Immutable versioning from the first commit** (ADR-007 — this is not a Phase B
  concern; retrofitting it corrupts data)
- Five field types: text, number, select, boolean, photo
- Web renderer
- No builder UI yet — templates are seeded as JSON

### A4 · Jobs, minimum viable (~40 hrs)
Job CRUD, job types, the status machine, one assignee, a scheduled date, required
forms, and the completion gate. A list view, not a dispatch board.

### A5 · Mobile skeleton (~60 hrs)
Expo shell, auth, PowerSync wired up (ADR-015 — adopted directly, no spike), my
day list, job detail, status transitions, native form renderer, photo capture.

**Phase A is done when** a job created on the web appears on a phone, is
completed offline with a form and photos, and syncs back.

---

## Phase B — Forms become genuinely good (~200 hrs · to month 16)

This is the pillar that carries the most value, so it deepens first.

- Drag-and-drop **form builder** with live preview
- Remaining field types: multiselect, date/time, signature, geolocation, instruction
- Conditional visibility, with a plain-language logic editor
- Publish flow with diff summary and pre-publish validation
- Submission review, verification, rejection
- **PDF export** in English and Arabic, with embedded Arabic font — verified with
  real Arabic content, never placeholder text
- `form_submission_values` projection for reporting

---

## Phase C — Field operations becomes real (~220 hrs · to month 22)

- Simple week calendar view, then drag-and-drop assignment
- Tasks within jobs; multiple assignees
- Job attachments, notes, completion signature
- Asset service history
- Recurring / preventive maintenance with holiday and weekend awareness
- Time entries
- Notifications: in-app, push, email
- Approval engine (ADR-012) — form verification first

---

## Phase D — Inventory and meetings deepen (~220 hrs · to month 28)

**Inventory:** items and categories, warehouse and van locations, the append-only
movement ledger with its rollup, transfer to van, consume-on-job from mobile,
returns and adjustments, stock list and movement history.

**Meetings:** meetings, internal attendees, RSVP, minutes, action items with
assignee and due date, conversion of an action item into a job.

---

## Phase E — Arabic, hardening, ship (~130 hrs · to month 32)

- Full Arabic RTL review of every screen, on real devices
- Offline scenario suite: the airplane-mode day, flaky network, kill-during-sync
- Load testing at a realistic single-tenant profile
- Backup and restore drill
- Security pass and dependency audit
- Onboarding flow and seed data
- Deployment runbook

---

## 3. Working practices for a project at this pace

The dominant risk here is **context loss between sessions**, not engineering
difficulty. Two hours on a Sunday, three weeks after the last session, is where
part-time projects quietly die.

- **End every session with a written note**: what you did, what is half-done,
  what is next. Ten minutes; it saves an hour of re-reading code.
- **Never leave the repository broken.** Commit working states only.
- **Keep the ADRs current.** In eight months you will not remember why the
  submission table looks like that.
- **One thing at a time.** Half-finished work across three modules is
  unrecoverable at this cadence.
- **Deployed from Phase A.** Something running on a real URL you can open on your
  phone is worth more than any amount of local progress.
- **Write the isolation test before the feature.** It is the one test category
  where a gap is not recoverable later.

## 4. Not needed yet

Deferred with no cost to the build, contrary to the original roadmap
(`16-open-questions.md`):

| | When it becomes real |
|---|---|
| Company registration, trade licence | ~1 month before the first paying customer |
| Payment gateway | With billing, post-v1 |
| WhatsApp BSP | Post-v1 |
| App Store presence | Phase E — Expo internal distribution is enough until then |
| Trademark search | Before any public launch or brand spend |
| Design partners | Deferred by decision; see §2 ordering rule |

## 5. What would change the plan

Stated so the trade-offs are visible rather than rediscovered:

- **More hours per week** is the only lever that shortens the calendar without
  removing features. 15 hrs/week roughly halves it; 25 roughly thirds it.
- **A real user appearing** should immediately reorder everything toward what
  they actually need. That beats this document.
- **Cutting to forms + mobile capture only** would put a sellable product in
  hand at roughly month 12 instead of month 32. Rejected by the product owner;
  recorded here because it remains true.
