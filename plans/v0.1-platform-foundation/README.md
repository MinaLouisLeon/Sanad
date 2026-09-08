# v0.1 — Platform Foundation

**Goal:** the platform exists, is genuinely multi-tenant, and is deployed. No
domain features yet.

Nothing about field service is built in this version. What is built is the layer
every later version sits on — and the parts of it that cannot be retrofitted:
tenant isolation, the audit trail, and the bilingual UI system.

## Ships with

- Multi-tenant Postgres with Row-Level Security, enforced in the database
- The **complete schema** for all modules (ADR-015: model full, build thin)
- Email and password authentication, sessions, invitations
- Four roles: owner, admin, dispatcher, engineer
- Append-only audit log
- English (default) and Arabic with full RTL
- Minimal design system and application shell
- CI pipeline and production deployment

## Does not ship with

Jobs, customers, forms, inventory, meetings, mobile app, notifications, billing.
All of those arrive later and all of them depend on this.

## Exit criteria

- [ ] Two tenants exist and the isolation suite proves neither can read the other
- [ ] A company can be created, a user invited, and both can sign in
- [ ] Roles restrict what each user sees and can do, enforced server-side
- [ ] Every write appears in the audit log with actor, before and after
- [ ] The UI renders correctly in English and in Arabic RTL
- [ ] CI runs the isolation suite on every commit and blocks on failure
- [ ] The app is live on a real URL and openable on a phone
- [ ] A database backup has been taken and a restore verified

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
