# v0.7 · Phase 2 — Build

## Tasks

### Database
- [ ] `meetings`, `meeting_attendees`, `meeting_minutes`, `action_items`
- [ ] `user_availability`, `time_off`
- [ ] `notification_templates`, `notification_preferences`, `notifications`,
      `notification_deliveries`
- [ ] RLS policies and isolation coverage on all of them

### Meetings domain
- [ ] Meeting service: create, update, cancel with reason
- [ ] Attendee service with RSVP
- [ ] Availability service — the single source, shared with job assignment
- [ ] Clash detection across meetings and job assignments
- [ ] Minutes service
- [ ] Action item service, including conversion to a job

### Notifications domain
- [ ] Event bus: domain modules publish, the notification service consumes.
      Domain code never calls an email or push provider directly
- [ ] Recipient resolution per event
- [ ] Preference and quiet-hours resolution
- [ ] Template rendering in the recipient's locale
- [ ] Channel adapters: in-app, push, email
- [ ] Delivery records, updated by provider webhooks
- [ ] Retry with backoff on transient failure
- [ ] Wire the v0.7 event catalogue into the existing modules

### Web
- [ ] Meetings: list, calendar, detail, create, edit
- [ ] Attendee picker showing availability inline
- [ ] RSVP controls
- [ ] Minutes editor
- [ ] Action items on the meeting and on the assignee's dashboard
- [ ] Convert action item to job
- [ ] Notification bell, list, mark as read
- [ ] Notification preferences screen
- [ ] Time off entry

### Mobile
- [ ] Push registration and permission request, asked at a sensible moment rather
      than on first launch
- [ ] Notification list
- [ ] My meetings, with RSVP
- [ ] Deep links from a notification to the relevant job or meeting

## Exit criteria

- [ ] A meeting runs end to end: created, responded to, minuted, action item raised
- [ ] An action item converts into a real job
- [ ] Availability reflects both meetings and job assignments
- [ ] All three channels deliver, in the recipient's language
