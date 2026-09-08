# 08 — Mobile Application & Offline Sync

The riskiest engineering component in the product. Field engineers work in
basements, plant rooms, lifts, remote sites and industrial areas with no
coverage. If the app fails offline, engineers revert to paper within a week and
the deployment is dead.

## 1. Principles

1. **Offline is the default assumption, not a degraded mode.** Every screen an
   engineer touches must function with the network disabled. The UI never blocks
   on a request.
2. **The device owns a real database**, not a cache. SQLite with the same
   entities the server has, queryable locally.
3. **Writes are queued, never lost.** A mutation recorded on the device survives
   app termination, OS kill, battery death and reinstall-free reboots, and
   replays exactly once.
4. **IDs are client-generated UUIDv7.** An engineer creates a submission, a time
   entry or a stock consumption offline with a final identity, so nothing needs
   remapping on sync.
5. **The user always knows the sync state.** A persistent, honest indicator:
   what is pending, what failed, when it last succeeded. Silent failure is worse
   than visible failure.

## 2. Scope of local data

Synced down to each device:

| Data | Scope | Refresh |
|---|---|---|
| My assignments and their jobs | Next 7 days + last 30 days | Continuous |
| Customers, sites, contacts | For my jobs, plus my branch | Daily + on change |
| Assets and recent service history | For my sites | Daily + on change |
| Form templates (published versions) | All active for my tenant | On publish |
| My draft and recent submissions | Last 30 days | Continuous |
| Item catalog | Full tenant catalog | Daily |
| My van stock balances | Mine only | Continuous |
| My meetings | Next 7 days | Continuous |
| Reference data | Job types, statuses, reasons, skills | On change |

Deliberately **not** synced: other engineers' jobs, costs and pricing, tenant-wide
reporting, and historical data beyond the windows above. This keeps the device
database small and limits data exposure if a phone is lost.

## 3. Sync engine

Per `01-architecture.md` §7, PowerSync is evaluated first in a two-week spike.
The following applies to either implementation.

**Down-sync:** streaming changes filtered by sync rules that enforce tenant and
user scope server-side. The device never requests data it is not entitled to; the
server never sends it.

**Up-sync:** an append-only local `mutations` table:

```
mutations
  id (uuid v7), entity_type, entity_id, operation (create|update|delete),
  payload jsonb, base_version, created_at, attempts, last_error,
  status (pending|sending|applied|conflicted|failed)
```

Replayed in creation order. Every request carries an idempotency key equal to the
mutation id, so a retry after an ambiguous network failure is safe.

**Attachments** (photos, signatures) are handled separately: files are written to
device storage immediately, registered in a local upload queue, and uploaded via
resumable background transfer directly to object storage using presigned URLs.
The submission itself syncs without waiting for photos; the record shows which
attachments are still uploading. Photos are compressed on-device (long edge
~1920 px, ~80% quality) — a 12 MP original over a 3G connection is a failed
upload.

## 4. Conflict resolution

Conflicts are rare in field service because engineers mostly write to records
only they touch. Handling them honestly still matters.

| Conflict | Resolution |
|---|---|
| Engineer edits a job reassigned to someone else while offline | Server rejects; engineer sees "this job was reassigned to X" with their captured data preserved and exportable |
| Two field updates on different fields | Field-level merge; both survive |
| Two field updates on the same field | Last-write-wins by server receipt time, with the losing value retained in the audit log |
| Stock consumption exceeding van balance | **Accepted.** Posts the movement, flags a negative balance for the inventory manager. Reality wins over the ledger's expectation |
| Form submission replayed | Idempotent on `client_uuid`; no duplicate |
| Job status transition now invalid | Server applies what it can, surfaces a clear conflict item; never silently discards |

Anything the server rejects lands in a **conflict inbox** on the device, with the
captured data intact and a human-readable explanation. Data an engineer typed is
never destroyed by a sync decision.

## 5. Application structure

Expo with expo-router. Screens, in the order an engineer uses them:

```
My Day       Today's jobs, timeline, offline badge, sync status
Job detail   Customer, site, asset, history, forms, parts, time, photos
Navigate     Hand off to the device map app
Form fill    Section-by-section, autosaving every field
Parts        Van stock, scan, consume, return
Signature    Capture, name, role
Complete     Checklist gate → completion
Assets       Scan a QR to open an asset's history and forms
Stock        My van, counts, transfers
Meetings     My meetings, RSVP, minutes
Profile      Devices, sync log, language, offline data usage
```

Field-condition requirements, all of which are cheap if designed in and expensive
to retrofit:
- Large touch targets — engineers wear gloves.
- High-contrast mode readable in direct sunlight.
- Full Arabic RTL layout, including the timeline and form flow.
- One-handed reachability; primary actions in the lower third.
- Battery discipline: no continuous GPS; sample at status transitions and on
  demand.
- Works on low-end Android — this is the actual device population in the region,
  not flagship iPhones. Test on a 3 GB RAM Android device as a matter of routine.

## 6. Security on the device

- Local SQLite encrypted at rest (SQLCipher or platform keystore-backed key).
- Biometric or PIN unlock, configurable per tenant.
- Remote wipe: revoking a device session clears local data on next launch, and a
  push-triggered wipe attempts it immediately.
- Automatic purge of local data on logout and after a configurable inactivity
  period.
- Screenshot blocking on sensitive screens where a tenant requires it.
- No customer data in device logs or crash reports; Sentry payloads scrubbed.

## 7. Push notifications

- Expo push → APNs/FCM, with per-device tokens.
- Delivered for: new assignment, schedule change, cancellation, urgent job,
  meeting reminder, form rejected.
- **Silent data pushes** wake the app to sync when new work is assigned, so an
  engineer who opens the app already has today's changes.
- Notification content is minimal — job number and type, never customer detail —
  because lock screens are visible to anyone nearby.

## 8. Testing this properly

Standard test suites do not catch offline bugs. Required:

- Automated tests running against a simulated network: offline, flaky (30% loss),
  high latency (2 s), and transitions between them mid-operation.
- **Airplane-mode scenario suite** run before every release: complete a full job
  offline including forms, photos, parts and signature, then reconnect and assert
  every artefact arrives exactly once.
- Kill-the-app-mid-sync tests.
- Clock-skew tests — device time is frequently wrong; the server timestamp is
  authoritative for ordering, while the device timestamp is retained as
  `captured_at`.
- Long-offline test: 72 hours offline with 20 completed jobs, then sync.
- Device matrix: two low-end Androids, one mid Android, two iPhones including a
  small screen.

## 9. Release management

- OTA updates via Expo Updates for JavaScript changes — critical, because app
  store review cannot be in the path of a field-blocking bug fix.
- Native builds only when native modules change.
- **Minimum supported version enforcement**: the server can refuse an app version
  older than a floor, with a clear upgrade prompt, because sync protocol changes
  cannot support ancient clients indefinitely.
- Staged rollout: internal → one pilot tenant → all.
