# v0.4 · Phase 4 — Release

## Tasks

### Backend
- [ ] Migrate and deploy the server changes first, so the app never meets an
      older API
- [ ] Confirm PowerSync production instance and sync rules are deployed
- [ ] Confirm storage bucket policies and presigned URL scoping in production

### Mobile build
- [ ] EAS build for iOS and Android, internal distribution
- [ ] Install on at least two real devices
- [ ] Configure OTA updates, and verify a rollback works **before** you need it
- [ ] Implement the minimum-supported-version check now, while there is only one
      version in the field — retrofitting it once devices are deployed is painful

### Smoke test on production
- [ ] Complete a real job offline on a real phone and confirm it arrives
- [ ] Confirm photos land in production storage and render on the web
- [ ] Confirm the audit log shows the mobile-sourced transitions

### Record
- [ ] Re-run the full `QA.md`, web and mobile
- [ ] Tag `v0.4.0`
- [ ] Write `RELEASE.md`, including the conflict decisions as implemented — this
      is the version whose behaviour you will most want to look up later
- [ ] Reconcile `docs/08-mobile-and-offline.md`
- [ ] Update `NOTES.md` for v0.5

## Exit criteria

- [ ] Backend deployed ahead of the app
- [ ] App installed and working on real devices
- [ ] Offline job completed on production and verified on the web
- [ ] OTA rollback verified
- [ ] `v0.4.0` tagged and release notes written
