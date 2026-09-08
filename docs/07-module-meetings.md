# 07 — Meetings & Calendar

## 1. Two distinct things

**Meetings** — internal coordination: team briefings, toolbox talks, safety
briefings, contract reviews, training, one-to-ones. Attendees are staff, possibly
with external guests.

**Customer appointments** — a commitment to attend a customer's site. These are
**jobs**, not meetings, and live in the field-ops module. Modelling them as
meetings splits operational reality across two systems.

The rule: if an engineer travels to a customer to do work, it is a job. If people
gather to talk, it is a meeting. A customer contract review at the customer's
office is a meeting with a customer link.

## 2. Unified availability

Meetings and job dispatch read from **one** availability source:

```
availability = working_hours(branch)
             − time_off(user)
             − holidays(tenant calendar)
             − existing job assignments
             − existing meeting attendance
```

This is why `user_availability` and `time_off` live in shared tables
(`03-data-model.md` §8). Without it, an engineer gets booked into a safety
briefing and a customer emergency at the same hour, and the dispatcher finds out
when the customer calls.

## 3. Capabilities

- Create with title, type, time, timezone, location and/or video link.
- Attendees: internal users, customer contacts, or free-form external emails.
- Required vs optional attendees; RSVP with accepted / declined / tentative.
- Availability finder: pick a duration and required attendees, see the first
  slots where everyone is genuinely free.
- Recurrence by RRULE with "this occurrence" / "this and future" editing.
- Agenda items with presenter and time allocation.
- Reminders: configurable per meeting, delivered on the attendee's preferred
  channels.
- Attachments and pre-reading.
- Cancellation with a mandatory reason, notifying all attendees.

## 4. Minutes and action items — the part that matters

Most meeting tools stop at scheduling, which is why most meeting tools are
abandoned. The value is in what happens afterwards.

- Minutes captured against the meeting, optionally per agenda item.
- **Action items** with assignee, due date and priority.
- An action item converts to a **job** in one click when it is field work, or to
  a task when it is internal — carrying its context and linking back to the
  meeting that created it.
- Open action items surface on the assignee's dashboard and in the next
  occurrence of a recurring meeting, so the follow-up is automatic.
- Minutes can be circulated as a PDF to attendees, including external ones.

This closes the loop from "we discussed it" to "it happened", which is the only
reason an operations team keeps using a meeting module.

## 5. Toolbox talks and safety briefings

A regionally important special case in contracting and FM: a short, recorded
safety briefing before work, with an attendance register.

- Template-driven topic content.
- Attendance captured by signature on mobile, on site.
- Produces a compliance record that clients and regulators ask for during audits.

Very small build (a meeting type plus a signature register), and a concrete
differentiator against generic FSM tools in the Gulf contracting market.

## 6. Calendar surfaces

- **My calendar** — jobs, meetings and time off in one view. Engineers must never
  need two calendars.
- **Team calendar** — supervisor's view of their team.
- **Resource calendar** — the dispatch board itself, shared with field-ops.
- **ICS feed** — a per-user, token-authenticated read-only subscription URL that
  works in Google Calendar, Outlook and Apple Calendar. Cheap to build, high
  perceived value, and it removes most of the pressure for full calendar sync in
  v1.

## 7. External calendar integration

- **v1:** ICS export feed only (read-only, one-way).
- **Phase 3:** two-way sync with Google Calendar and Microsoft 365 via their
  APIs — OAuth per user, incremental sync tokens, echo-loop suppression,
  deletion semantics, and per-tenant admin consent. This is genuinely fiddly and
  does not belong in a launch already carrying four modules.
- Video links: paste any URL in v1; native Zoom/Teams/Meet creation in Phase 3.

## 8. Notifications

Invitation on create; reminders at configured intervals; update notices on time
or location change; cancellation notices; minutes circulated on publish; action
item assigned and due-soon alerts. All respect the recipient's channel
preferences and quiet hours (`11-notifications-and-i18n.md`).
