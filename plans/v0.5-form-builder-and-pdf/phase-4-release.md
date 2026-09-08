# v0.5 · Phase 4 — Release

## Tasks

- [ ] Deploy the PDF renderer service and confirm it is reachable only by the
      application, not publicly
- [ ] Confirm fonts are present in the deployed image — a missing font shows up
      as boxes in Arabic and is easy to miss in review
- [ ] Migrate and deploy the application
- [ ] Smoke test in production: build a form in the builder, publish it, complete
      it on a phone, verify it, download the PDF in **both** languages
- [ ] Confirm PDF generation appears in monitoring and does not time out
- [ ] Re-run the full `QA.md`
- [ ] Tag `v0.5.0`
- [ ] Write `RELEASE.md`
- [ ] Reconcile `docs/04-module-forms.md`
- [ ] Update `NOTES.md` for v0.6

## Exit criteria

- [ ] PDF service deployed with fonts verified in the running container
- [ ] Full round trip proven in production, in both languages
- [ ] `v0.5.0` tagged and release notes written
