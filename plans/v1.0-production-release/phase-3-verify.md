# v1.0 · Phase 3 — Verify

## Tasks

### Onboarding — test with someone who has not seen it
- [ ] Sign up as a genuinely new tenant, using no prior knowledge
- [ ] Complete the wizard and create the first job without consulting anything
- [ ] Watch someone else do it, and say nothing while they do. Where they hesitate
      is the defect list
- [ ] Verify demo data is added and removed cleanly, leaving nothing behind
- [ ] Confirm activation events are recorded correctly

### Disaster recovery — the rehearsal, not the theory
- [ ] Restore a full backup into a fresh environment and confirm the data is intact
- [ ] **Time it against the RTO target** and record the actual figure
- [ ] Restore a single tenant's data into a scratch database, following the runbook
      exactly as written. If the runbook cannot be followed, the runbook is wrong
- [ ] Verify point-in-time recovery to a chosen moment

### Alerting
- [ ] Deliberately trigger each alert and confirm it fires and reaches you
- [ ] Confirm each links to a runbook that is actually usable
- [ ] Confirm no alert is so noisy it will be ignored within a week

### Full regression
- [ ] Complete `QA.md`, every section, both languages, web and mobile
- [ ] Full offline suite
- [ ] Full isolation suite
- [ ] Load test at expected launch volume

### Security
- [ ] Final review of authentication, authorization and file handling
- [ ] Confirm no secrets in the repository or in client bundles
- [ ] Confirm production configuration differs correctly from development

### Store readiness
- [ ] Both builds approved, or rejection feedback addressed and resubmitted
- [ ] Install from the store on a clean device and complete a job

## Exit criteria

- [ ] Onboarding completed by someone other than you, unaided
- [ ] Restore rehearsed and timed against RTO
- [ ] Every alert fires and resolves to a usable runbook
- [ ] Full regression, offline and isolation suites green
- [ ] Store builds approved and installable
