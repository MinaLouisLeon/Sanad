# v0.6 · Phase 4 — Release

## Tasks

- [ ] Review migrations, especially the grant changes on `stock_movements`
- [ ] Migrate and deploy the backend
- [ ] Ship the mobile update with the new sync scope; confirm existing devices
      hydrate the new tables cleanly rather than erroring
- [ ] Smoke test in production: receive stock, transfer to a van, consume it on a
      job from a phone while offline, reconnect, confirm the ledger and the rollup
- [ ] Run the rollup rebuild against production and confirm it changes nothing
- [ ] Re-run the full `QA.md`
- [ ] Tag `v0.6.0`
- [ ] Write `RELEASE.md`
- [ ] Reconcile `docs/06-module-inventory.md`
- [ ] Update `NOTES.md` for v0.7

## Exit criteria

- [ ] Deployed, with existing devices migrating cleanly to the new sync scope
- [ ] Production rollup rebuild produces no change — proof the ledger is authoritative
- [ ] `v0.6.0` tagged and release notes written
