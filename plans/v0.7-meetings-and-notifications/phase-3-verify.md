# v0.7 · Phase 3 — Verify

## Tasks

### Meetings
- [ ] Full lifecycle: create, RSVP, minute, cancel
- [ ] Cancellation notifies every attendee
- [ ] Action item conversion carries context and links back to the meeting
- [ ] Availability: a person on a job is unavailable for an overlapping meeting,
      and the reverse
- [ ] Time off removes availability
- [ ] Visibility rules hold — a private meeting is not listed for non-attendees

### Notifications
- [ ] Every event in the catalogue produces the expected notification
- [ ] Preferences are honoured, including opting a channel off entirely
- [ ] Quiet hours suppress non-urgent notifications and pass emergency ones
- [ ] **Language: an Arabic-preferring recipient receives Arabic even when the
      actor was working in English**
- [ ] A missing template falls back to English and raises an alert; nothing is
      dropped silently
- [ ] Delivery records reflect actual outcomes, including bounces
- [ ] Retries do not produce duplicate notifications
- [ ] Push arrives on a real device, and its deep link opens the right screen
- [ ] Push content carries no customer detail

### Email deliverability
- [ ] SPF, DKIM and DMARC verified as passing
- [ ] Test sends to at least three providers land in the inbox, not spam
- [ ] Arabic subject lines and bodies render correctly in real mail clients

### Isolation and access
- [ ] New tables and routes added to the isolation suite
- [ ] A notification cannot be read by another tenant's user
- [ ] Event fan-out never crosses a tenant boundary

### Interface
- [ ] QA in English and Arabic RTL, web and mobile
- [ ] Extend `QA.md`

## Exit criteria

- [ ] Meeting lifecycle and action item conversion proven
- [ ] Availability shared correctly between meetings and jobs
- [ ] Notifications delivered on all three channels in the right language
- [ ] Email authentication passing and inbox placement confirmed
- [ ] Isolation extended and green
