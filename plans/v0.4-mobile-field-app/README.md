# v0.4 — Mobile Field App

**Goal:** a field engineer can do a full day's work on a phone, with no signal,
and everything arrives when they reconnect.

The largest and riskiest version in the plan. It is also the one that makes Sanad
a field-service product rather than a web database.

## Ships with

- Expo application: authentication, navigation, design system
- PowerSync integration and a local SQLite database (ADR-015 — adopted directly,
  no evaluation spike)
- My Day: today's assigned jobs
- Job detail: customer, site, asset, history, navigate, call
- Status transitions from the phone, with location captured at each
- Native form renderer sharing `forms-engine` with the web
- Photo capture with on-device compression and resumable background upload
- Signature capture
- Offline queue with an honest sync indicator
- Basic conflict inbox

## Does not ship with

Inventory on mobile (v0.6), meetings (v0.7), push notifications (v0.7), barcode
scanning, App Store distribution. Distribution in this version is Expo internal
builds — the store can wait until v1.0.

## Exit criteria

- [ ] An engineer signs in and sees today's jobs
- [ ] **The airplane-mode day passes:** aeroplane mode on, complete several jobs
      with forms, photos and signatures, reconnect, and every artefact arrives
      exactly once with nothing lost
- [ ] Killing the app mid-sync loses nothing
- [ ] 72 hours offline with accumulated work syncs correctly
- [ ] Photos upload in the background and survive the app being terminated
- [ ] The sync indicator honestly reflects what is pending and what failed
- [ ] Local data is encrypted at rest and cleared on sign-out

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
