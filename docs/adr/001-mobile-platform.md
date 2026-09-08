# ADR-001 — Expo / React Native for the field application

**Status:** Accepted · 2026-09-08

## Context
Field engineers work in basements, plant rooms and remote sites without
connectivity. The app must capture photos, signatures, GPS and barcodes, and it
must work fully offline. A PWA, a native app, or a hybrid were all possible.

## Options
- **PWA only** — one codebase, cheapest, but iOS background sync and push are
  unreliable and camera access is degraded.
- **Expo / React Native** — near-native capability, shares TypeScript domain
  packages with the web app, roughly 40% more effort than a PWA.
- **Fully native (Swift + Kotlin)** — best capability, two codebases, not
  viable for a team of this size.

## Decision
Expo / React Native, sharing `packages/core`, `packages/forms-engine` and
`packages/api-contract` with the web application.

## Consequences
- Reliable offline SQLite, background uploads, push, camera and barcode scanning.
- App store review is in the release path; mitigated by Expo OTA updates for
  JavaScript changes.
- A dedicated mobile engineer is required for roughly 13 weeks.
- Offline-first data design is mandatory from the first commit — it cannot be
  retrofitted.
