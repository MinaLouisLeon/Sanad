# v0.7 · Phase 1 — Decide

## Inputs

- `docs/07-module-meetings.md`, `docs/11-notifications-and-i18n.md`

## Tasks

### Meetings
- [ ] Confirm the boundary: if someone travels to a customer to do work it is a
      **job**, not a meeting. A meeting is people gathering to talk
- [ ] Decide the meeting types shipping now
- [ ] Decide the RSVP states and whether a non-response blocks anything
- [ ] Decide who may see which meetings — the visibility model
- [ ] Decide the action item lifecycle and what converting one to a job carries over

### Availability
- [ ] Confirm that meetings and job dispatch read one availability source
- [ ] Decide how availability is computed: working hours, minus time off, minus
      holidays, minus job assignments, minus meeting attendance
- [ ] Decide whether a clash is blocked or merely warned (recommendation: warn
      and require confirmation — the dispatcher often knows better than the system)

### Notifications
- [ ] Decide the event catalogue for v0.7 — a deliberately small set
- [ ] Decide which events default to which channels
- [ ] Decide the quiet hours default and which events may override it
- [ ] Decide template storage, variable resolution and escaping
- [ ] Decide the email provider and configure the sending domain with SPF, DKIM
      and DMARC. This has propagation delay, so start it early in the phase
- [ ] Decide push registration and token lifecycle
- [ ] Decide what a push notification may contain — minimal, since lock screens
      are visible to bystanders
- [ ] Decide delivery record retention

### Language
- [ ] Confirm notifications render in the recipient's locale, not the actor's
- [ ] Decide the fallback when a template is missing in a locale: fall back to
      English and raise an alert rather than dropping the message silently

## Exit criteria

- [ ] Meeting model and visibility decided
- [ ] Availability computation specified
- [ ] Event catalogue and channel defaults fixed
- [ ] Email domain authentication started
