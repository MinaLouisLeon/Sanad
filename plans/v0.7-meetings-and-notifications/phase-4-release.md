# v0.7 · Phase 4 — Release

All four pillars are live after this release. Worth marking.

## Tasks

- [ ] Confirm the sending domain's DNS records are live in production
- [ ] Confirm push credentials are configured for production builds
- [ ] Migrate and deploy the backend
- [ ] Ship the mobile update
- [ ] Smoke test in production: create a meeting, respond, minute it, raise an
      action item, convert it to a job, and confirm the assignee is notified on
      every enabled channel
- [ ] Send one notification to a real Arabic-preferring account and read it on a
      real phone
- [ ] Confirm delivery records are populating and provider webhooks are arriving
- [ ] Re-run the full `QA.md`
- [ ] Tag `v0.7.0`
- [ ] Write `RELEASE.md`, noting that all four pillars are now present
- [ ] Reconcile `docs/07-module-meetings.md` and `docs/11-notifications-and-i18n.md`
- [ ] Update `NOTES.md` for v0.8

## Exit criteria

- [ ] Deployed, with email authentication and push both working in production
- [ ] Cross-module flow proven live: meeting → action item → job → notification
- [ ] `v0.7.0` tagged and release notes written
