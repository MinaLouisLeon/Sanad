# 05 — Field Operations: Jobs, Dispatch & Scheduling

The operational core. Everything else — forms, parts, time, evidence — attaches
to a job.

## 1. Entity separation

**Job** (work order) — the unit of customer-facing work, the thing with a number,
an SLA, a cost and an invoice.
**Task** — a step within a job. Each may require its own form.
**Assignment** — a person on a job/task for a time window.

Keeping these separate is what allows two engineers on different days, a lead
plus a helper, or a job split across visits. Collapsing them into one table works
until the first two-person job, then requires re-modelling everything downstream.

## 2. Job lifecycle

```
                 ┌─────────────── cancelled ───────────────┐
                 │                                          │
draft ─▶ scheduled ─▶ dispatched ─▶ en_route ─▶ in_progress ─▶ completed ─▶ verified ─▶ closed
                 ▲         │            │            │  ▲          │
                 │         │            │            ▼  │          │
                 └─────────┴────────────┴──── on_hold ──┘          │
                 │                                                  │
                 └──────────── failed_visit ────────────────────────┘
                                    │
                                    └──▶ creates follow-up job
```

| Status | Meaning | Entered by |
|---|---|---|
| `draft` | Being prepared, not yet real work | Dispatcher |
| `scheduled` | Has a time window, may not have a person | Dispatcher / recurrence engine |
| `dispatched` | Assigned and sent to an engineer's device | Dispatcher |
| `en_route` | Travelling — starts travel time tracking | Engineer (mobile) |
| `in_progress` | On site, working | Engineer (mobile) |
| `on_hold` | Blocked: awaiting part, access, or approval — reason required | Engineer / supervisor |
| `completed` | Work done, forms submitted, signature captured | Engineer |
| `verified` | Reviewed and approved by supervisor | Supervisor |
| `closed` | Financially and operationally final; immutable | System / admin |
| `cancelled` | Called off; reason required | Dispatcher / admin |
| `failed_visit` | Attended, could not complete — reason required | Engineer |

Rules:
- Transitions are validated by an explicit state machine in `packages/core/field-ops`.
  Invalid transitions are rejected, not corrected.
- Every transition writes to `job_status_history` with actor, timestamp, GPS,
  source and reason.
- `completed` is blocked until all **required** forms are submitted and any
  mandatory signature is captured. This single rule is what eliminates the "job
  sheet never came back" problem.
- `failed_visit` requires a reason from a configurable list and offers immediate
  rescheduling. It is a headline KPI, not a variant of cancellation.
- `closed` is immutable; reopening requires `admin` plus a stated reason and is
  audited.

## 3. Creating work

| Source | Mechanism |
|---|---|
| Manual | Dispatcher creates from customer, site or asset |
| Recurring / preventive | RRULE-driven generation (§6) |
| From a failed inspection | A critical form failure auto-creates a corrective job |
| From a meeting action item | One-click conversion |
| API / integration | Third-party systems (`12-api-and-integrations.md`) |
| Public intake link | A tenant-specific URL or QR sticker on equipment that lets a site contact report a fault. Creates a `draft` job. Very cheap, disproportionately valued |

## 4. The dispatch board

The dispatcher's entire working day. It deserves disproportionate design effort.

- **Timeline view**: engineers as rows, time as columns; day / 3-day / week.
- **Map view**: unassigned jobs and engineer positions, for proximity decisions.
- **List view**: dense, filterable, bulk actions.
- Drag to assign; drag edges to resize duration; drag between engineers to
  reassign.
- Unassigned queue docked alongside, sorted by SLA urgency.
- Live updates: another dispatcher's changes and engineer status changes appear
  without refresh.
- Conflict indicators: double-booking, travel time that cannot be met, outside
  working hours, engineer on leave, expired certification.
- Colour by status, priority or job type; toggleable.
- Filters: branch, team, skill, job type, customer, SLA-at-risk.
- Keyboard-driven for power users; the dispatcher does this hundreds of times a
  day and the mouse is the bottleneck.
- **Full RTL support** — the timeline runs right-to-left in Arabic, which is a
  genuinely non-trivial rendering problem and must be designed in, not patched on.

## 5. Assignment intelligence

Ordered by build complexity; each layer is independently useful.

1. **Manual** — dispatcher decides. Always available; never remove it.
2. **Eligibility filtering** — only offer engineers who hold the required skills,
   whose certifications are valid on the job date, who are on shift and not on
   leave. Hard block with an explanation, not a warning.
3. **Availability check** — no overlapping assignment or meeting.
4. **Proximity ranking** — sort candidates by distance from their previous job.
   Delivers most of the practical benefit of optimization for a fraction of the
   cost.
5. **Suggested schedule** — the system proposes a full day per engineer; the
   dispatcher accepts or edits. Phase 3.
6. **Route optimization** — true multi-stop VRP with time windows via a routing
   provider. Phase 4.

Never auto-assign without a human confirmation step in v1. Dispatchers hold
context the system cannot see, and silently reassigning their work destroys
trust in the tool immediately.

## 6. Recurring and preventive maintenance

- Defined by RRULE plus a tenant timezone, attached to an asset, site or contract.
- A background job generates upcoming jobs on a rolling horizon (default 60 days
  ahead, configurable), so dispatchers can plan and engineers see them.
- Changing a recurrence offers "this occurrence" / "this and future" semantics.
- **Holiday and weekend awareness**: an occurrence landing on a Friday in Saudi
  Arabia, or on Eid, shifts according to a configurable policy (skip, before,
  after). Getting this wrong generates hundreds of wrong jobs and is the fastest
  way to lose a regional customer's confidence.
- Contract-linked recurrences track entitlement: "12 visits per year, 7
  consumed" — the basis for renewal conversations.

## 7. SLA management

- Policies attach to a contract, customer or job type, and vary by priority.
- Two clocks: **respond by** (someone is en route / on site) and **resolve by**
  (job completed).
- Business-hours-aware: clocks pause outside the tenant's working hours,
  weekends and holidays — this is where the regional calendar configuration
  becomes financially significant.
- Pausing: `on_hold` for customer-caused reasons pauses the resolve clock;
  internal reasons do not. This distinction is contentious with customers, so it
  is configurable and fully logged.
- Escalation chain: at 50% / 80% / 100% of the window, notify assignee →
  supervisor → manager.
- Breaches are recorded on the job, reported per customer and per contract, and
  drive credit calculations.

## 8. Time and cost

- Time entries distinguish travel, work and break, captured automatically at
  status transitions with manual override.
- Optional geofenced clock-in: entering the site radius prompts arrival. Must be
  disclosed to staff (see `09-security-and-compliance.md` §7).
- Job cost = labour (hours × role rate) + parts (from stock movements at cost) +
  expenses. Visible to supervisors and above; hidden from engineers.
- Billable vs non-billable flags per job and per line, ready for the Phase 3
  invoicing module.

## 9. The engineer's job flow (mobile)

Optimized for one hand, gloves, sunlight, and no signal:

```
My Day  →  Job card  →  Navigate  →  Arrive  →  Checklist & forms
                                                      ↓
        Close  ←  Customer signature  ←  Photos  ←  Parts used
```

- Today's jobs sorted by time, with an offline indicator per job.
- One tap to navigate (hands off to the device's map app), one tap to call the
  site contact.
- Asset history visible on site — what was done last time, by whom, what failed.
- Required forms surfaced inline; the job cannot be completed until they are done.
- Parts consumed are picked from the engineer's own van stock, scannable.
- Signature captured on the device, then the customer receives the PDF
  automatically by email or WhatsApp.
- Everything works offline; a sync indicator shows what is pending upload.

## 10. Reporting for this module

Jobs completed vs scheduled; first-time fix rate; failed-visit rate and reasons;
SLA compliance by customer and contract; average response and resolution time;
engineer utilization (productive hours ÷ available hours); travel time as a
proportion of the day; job cost and margin by type, customer and engineer;
overdue preventive maintenance.

First-time fix rate and utilization are the two numbers operations directors are
judged on. Put them on the default dashboard.
