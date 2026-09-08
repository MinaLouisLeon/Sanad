# v0.7 — Meetings & Notifications

**Goal:** the fourth pillar arrives, and the system starts telling people things
instead of waiting to be checked.

All four pillars of ADR-004 are present when this version ships.

## Ships with

**Meetings**
- Meetings with internal attendees and RSVP
- Minutes
- Action items with assignee and due date
- One-click conversion of an action item into a job
- Shared availability, so a person cannot be booked into a meeting and a job at
  the same hour

**Notifications**
- Event-driven notification service
- Channels: in-app, push (mobile), email
- Per-user preferences and quiet hours
- Bilingual templates, rendered in the **recipient's** locale

## Does not ship with

Meeting recurrence, the availability finder, ICS feed, toolbox talks, external
attendees, agenda items, calendar sync. WhatsApp and SMS channels. All ADR-015
depth cuts.

## Exit criteria

- [ ] A meeting is created, attendees respond, minutes are recorded
- [ ] An action item becomes a job carrying its context
- [ ] A person already assigned to a job shows as unavailable for a meeting
- [ ] Notifications reach in-app, push and email
- [ ] A notification renders in the recipient's own language, not the sender's
- [ ] Quiet hours are respected, and only emergency events override them
- [ ] Every delivery attempt is recorded with its outcome

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
