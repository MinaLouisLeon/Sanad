# v0.2 · Phase 4 — Release

## Tasks

- [ ] Review migrations for lock behaviour on populated tables; create indexes
      `CONCURRENTLY`
- [ ] Run migrations against production
- [ ] Deploy
- [ ] Smoke test on production: create a customer, site, asset and job; assign it;
      move it through to completion; check the history
- [ ] Confirm Sentry is receiving events from the new routes
- [ ] Re-run the full `QA.md`, not only the new sections
- [ ] Tag `v0.2.0`
- [ ] Write `RELEASE.md`: what shipped, what was deferred, what surprised you
- [ ] Reconcile `docs/05-module-field-ops.md` with what was actually built
- [ ] Update `NOTES.md` for v0.3

## Exit criteria

- [ ] Deployed and smoke tested in production
- [ ] Full QA script re-run and passing
- [ ] `v0.2.0` tagged and release notes written
- [ ] Documentation reconciled
