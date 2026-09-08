# v1.0 · Phase 2 — Build

## Tasks

### Onboarding
- [ ] Public signup: company, owner account, email verification
- [ ] Onboarding wizard: company details, first users, first customer and site,
      first asset, first form from a starter template, first job
- [ ] Demo data generator and a clean one-click removal
- [ ] Contextual empty states with a next action on every main screen
- [ ] Activation instrumentation

### Operations
- [ ] Alerting configured against the decided thresholds
- [ ] Every alert linked to its runbook
- [ ] Status page live
- [ ] Uptime checks against sign-in, the job list and mobile sync, from a Gulf region
- [ ] Business dashboard: jobs created and completed, active devices, forms
      submitted, sync failures. A sudden drop here detects a bad deploy faster
      than any infrastructure alert
- [ ] Backup schedule confirmed and retention applied

### Runbooks
Write each one, in `docs/`:
- [ ] Sync backlog growing
- [ ] Database connections exhausted
- [ ] Storage quota exhausted
- [ ] A tenant reports missing data
- [ ] Suspected cross-tenant exposure
- [ ] Mobile release rollback
- [ ] Restoring a single tenant's data
- [ ] Rotating a compromised secret

### Support
- [ ] Support channel connected
- [ ] In-app help and a contact route
- [ ] A tenant health view showing plan, version, recent errors and last sync per
      device, so a support conversation does not begin with twenty questions

### Legal and content
- [ ] Terms, privacy policy and DPA published, in both languages
- [ ] Sub-processor list published
- [ ] A minimal marketing page explaining what Sanad is and who it is for

### Distribution
- [ ] Production builds submitted to both stores
- [ ] Store listings in English and Arabic, with real screenshots

## Exit criteria

- [ ] Signup and onboarding complete end to end
- [ ] Alerts, status page and dashboards live
- [ ] All runbooks written
- [ ] Legal pages published
- [ ] Store submissions in review
