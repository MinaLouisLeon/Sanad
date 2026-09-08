# v1.0 · Phase 1 — Decide

## Inputs

- `docs/13-observability-and-ops.md`, `docs/09-security-and-compliance.md`
- v0.8 released and clean

## Tasks

### Onboarding
- [ ] Decide the signup flow and what is asked at each step. Ask for the minimum:
      company name, country, timezone. Everything else can come later
- [ ] Decide the onboarding wizard's steps and their order
- [ ] Decide the demo dataset: what it contains and how it is removed cleanly
- [ ] Decide what "activated" means for a new tenant, and instrument it. A tenant
      that has completed real jobs with forms behaves very differently from one
      that only signed in
- [ ] Decide the empty-state guidance on every main screen

### Operations
- [ ] Decide the alert set and the threshold for each. Alert on symptoms users
      feel, not on causes
- [ ] Decide severity levels and the response expected at each
- [ ] Decide which runbooks must exist before launch
- [ ] Decide the status page provider and what it reports
- [ ] Decide backup retention and confirm the RPO and RTO targets are achievable
- [ ] Decide the on-call arrangement, however informal

### Support
- [ ] Decide the support channel. WhatsApp Business is the realistic answer for
      this market; email alone will not be used
- [ ] Decide support hours — Sunday to Thursday, Gulf time
- [ ] Decide what a support request must include and how tenant context reaches you
- [ ] Decide where help content lives

### Legal
- [ ] Decide who writes the terms, privacy policy and data processing agreement
- [ ] Decide the retention policy stated to customers
- [ ] Decide the sub-processor list to publish

### Distribution
- [ ] Decide the store listing: name, description, screenshots, in both languages
- [ ] Decide the app's privacy declarations for both stores — location and camera
      use must be declared accurately
- [ ] Note that store review takes real time and can reject on first submission.
      Start it in this phase rather than in Phase 4

## Exit criteria

- [ ] Onboarding flow specified
- [ ] Alert set and runbook list decided
- [ ] Support channel decided and reachable
- [ ] Legal documents commissioned or drafted
- [ ] Store submissions started
