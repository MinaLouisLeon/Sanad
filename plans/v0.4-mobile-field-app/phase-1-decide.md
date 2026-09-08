# v0.4 · Phase 1 — Decide

## Inputs

- `docs/08-mobile-and-offline.md` — read in full
- ADR-001 (Expo), ADR-015 (PowerSync adopted directly)

## Tasks

### Sync
- [ ] Set up a PowerSync instance and connect it to the database
- [ ] Write the **sync rules**, and treat them as a security boundary: they decide
      what reaches a device. An incorrect rule here is a cross-tenant leak in the
      most sensitive component in the system
- [ ] Decide exactly what syncs down: assignments for the next 7 and last 30 days,
      their jobs, related customers, sites and assets, published form templates,
      own submissions for 30 days, reference data. Nothing else
- [ ] Decide what never syncs: other engineers' jobs, any cost or price data,
      tenant-wide reporting
- [ ] Decide how tenant and user scope are enforced inside the sync rules
- [ ] Decide the local schema — mirroring server tables, not a bespoke shape

### Writes and conflicts
- [ ] Confirm client-generated UUIDv7 for every entity created on the device
- [ ] Decide the idempotency key strategy for replayed mutations
- [ ] Fix the conflict matrix from `docs/08-mobile-and-offline.md` §4, in
      particular: a job reassigned while the engineer was offline
- [ ] Decide what the conflict inbox shows and what the engineer can do about it.
      **Data the engineer typed is never silently discarded**
- [ ] Decide clock handling: the server timestamp orders events, the device
      timestamp is retained as `captured_at`. Device clocks are frequently wrong

### Attachments
- [ ] Decide photo compression: target long edge and quality
- [ ] Decide the upload mechanism — presigned direct-to-storage, resumable,
      surviving app termination
- [ ] Decide whether a submission may sync before its photos finish uploading
      (recommendation: yes, with the record showing what is still pending)
- [ ] Decide EXIF policy: strip everything except location, and only when the
      tenant has enabled location capture

### Security
- [ ] Decide local database encryption and where the key is held
- [ ] Decide biometric or PIN unlock and whether it is optional
- [ ] Decide sign-out behaviour: local data purged
- [ ] Decide refresh token lifetime — long, because an engineer must not be
      signed out mid-shift in a basement
- [ ] Confirm no customer data reaches device logs or crash reports

### Application
- [ ] Decide the navigation structure
- [ ] Decide the offline indicator's placement and vocabulary
- [ ] Confirm the copy constraint: simple English, short labels, no idiom,
      icon-supported, large touch targets for gloved hands
- [ ] Decide the device test matrix, weighted towards low-end Android

## Exit criteria

- [ ] PowerSync running with sync rules written and tenant-scoped
- [ ] Conflict matrix decided
- [ ] Attachment pipeline decided
- [ ] Device security decisions recorded
- [ ] ADRs written for anything diverging from `docs/08-mobile-and-offline.md`
