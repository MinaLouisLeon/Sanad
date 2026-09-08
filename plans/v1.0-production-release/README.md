# v1.0 — Production Release

**Goal:** a real company that has never spoken to you can sign up and run their
field operations on Sanad.

The software has been in production since v0.1. What v1.0 adds is everything
around it that makes it a **product** rather than a deployed application:
somebody can arrive, understand it, set it up, and get help when it breaks.

## Ships with

- Self-service signup and a guided onboarding wizard
- Optional demo data, removable in one click
- Verified backups and a rehearsed restore procedure
- Monitoring, alerting and a status page
- Operational runbooks
- Legal pages: terms, privacy, data processing
- A support path a customer can actually use
- App Store and Play Store distribution
- The first real tenant onboarded

## Does not ship with

Billing — customers are invoiced manually until there is a reason not to
(ADR-015). Also absent: customer portal, public API, platform admin console,
WhatsApp and SMS. All deferred past v1.0.

## Exit criteria

- [ ] A stranger can sign up, complete onboarding and create their first job
      without being told how
- [ ] The mobile app is installable from both stores
- [ ] A restore from backup has been performed and timed against the RTO target
- [ ] Alerts fire on real conditions and each links to a runbook
- [ ] A real company is using it for real work

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
