# v0.4 · Phase 3 — Verify

Standard test suites do not catch offline bugs. These scenarios do.

## Tasks

### The airplane-mode day — the blocking gate
- [ ] Sync a day's work while online, then enable aeroplane mode
- [ ] Complete several jobs: status transitions, forms, multiple photos each,
      signatures, notes
- [ ] Reconnect
- [ ] Assert **every** artefact arrived, **exactly once**, attached correctly,
      with nothing lost and nothing duplicated
- [ ] Repeat with the app force-killed part-way through the offline period
- [ ] Repeat with the app force-killed during the sync itself

### Network conditions
- [ ] Flaky network: roughly 30% packet loss during sync
- [ ] High latency: 2-second round trips
- [ ] Network dropping mid-request, repeatedly
- [ ] Transition between offline and online during an active upload

### Duration and volume
- [ ] 72 hours offline with twenty completed jobs, then sync
- [ ] A submission with the maximum permitted photos
- [ ] Confirm local database size stays reasonable and old data is pruned

### Conflicts
- [ ] Job reassigned while the engineer was offline: their data is preserved and
      the conflict is explained, never silently dropped
- [ ] The same submission replayed twice creates one record
- [ ] A status transition that became invalid surfaces a clear conflict
- [ ] Device clock set wrong: ordering still correct, `captured_at` preserved

### Security
- [ ] Local database unreadable without the app
- [ ] Sign-out purges local data
- [ ] No customer data in logs or Sentry payloads
- [ ] Sync rules verified: a device receives **only** its own user's scoped data.
      Add this to the isolation suite

### Devices
- [ ] Two low-end Android devices — the real field population
- [ ] One mid-range Android, two iPhones including a small screen
- [ ] Sunlight legibility check on a real screen, outdoors
- [ ] Glove test on the primary actions
- [ ] Arabic RTL pass on every screen, on a device

## Exit criteria

- [ ] Airplane-mode day passes, including both force-kill variants
- [ ] Flaky and high-latency scenarios pass
- [ ] 72-hour accumulation syncs correctly
- [ ] Conflict cases behave as decided, with no data loss
- [ ] Device matrix passed
- [ ] Sync-rule isolation proven
