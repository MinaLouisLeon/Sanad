# v0.4 · Phase 2 — Build

Build in this order. Sync before screens — screens built against a sync layer
that does not yet work get rewritten.

## Tasks

### Application shell
- [ ] Expo app in `apps/mobile` with expo-router
- [ ] `packages/ui-native` design system: large targets, high contrast, RTL-aware
- [ ] i18n wired to the shared catalogs, English default, Arabic RTL
- [ ] Authentication: sign in, token refresh, sign out with local purge
- [ ] Encrypted local database with biometric or PIN unlock

### Sync layer — before any feature screen
- [ ] PowerSync client connected, initial hydration working
- [ ] Local schema mirroring the synced tables
- [ ] Mutation queue: append-only, survives termination, replays in order
- [ ] Idempotency keys on every mutation
- [ ] Reconnection and backoff
- [ ] Sync state exposed to the UI: last success, pending count, failures
- [ ] Conflict inbox storage and surfacing

### Attachments
- [ ] Camera capture and on-device compression
- [ ] Local file storage and an upload queue
- [ ] Resumable background upload to presigned URLs
- [ ] Per-attachment status visible on the record
- [ ] Signature capture producing strokes plus a rendered image

### Screens
- [ ] My Day: today's jobs, offline badge, sync status, pull to refresh
- [ ] Job detail: customer, site, asset, description, history, required forms
- [ ] Navigate — hand off to the device map app; Call — hand off to the dialer
- [ ] Status transitions with location capture, and reason prompts where required
- [ ] Native form renderer using `packages/forms-engine`
- [ ] Field components for every v0.3 type, plus photo and signature
- [ ] Form autosave to local storage on every field change
- [ ] Job completion flow with the required-forms gate enforced locally
- [ ] Sync screen: pending items, failures, conflict inbox
- [ ] Profile: language, device, sign out

### Server side
- [ ] Endpoints accepting mutations with idempotency keys
- [ ] Conflict detection and structured conflict responses
- [ ] Presigned upload issuance, scoped to the tenant
- [ ] Attachment registration on receipt

## Exit criteria

- [ ] Sign in, see today's jobs, complete one online end to end
- [ ] The same flow completes fully offline and syncs on reconnect
- [ ] Photos and signatures arrive attached to the right submission
- [ ] The sync indicator matches reality
