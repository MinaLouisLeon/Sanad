# v0.1 · Phase 4 — Release

The first release, so it establishes the ritual every later version repeats.

## Tasks

### Production readiness
- [ ] Production Supabase project separate from development
- [ ] Environment variables set in Vercel; nothing secret in the repository
- [ ] Custom domain with TLS
- [ ] Security headers: HSTS, CSP, referrer policy, permissions policy
- [ ] Rate limiting on authentication routes
- [ ] Sentry connected for web, with releases tagged
- [ ] Structured logging carrying `request_id`, `tenant_id`, `user_id`

### Backups — verified, not merely enabled
- [ ] Point-in-time recovery confirmed active
- [ ] Take a backup
- [ ] **Restore it into a scratch database and confirm the data is intact.**
      An untested backup is not a backup
- [ ] Record the restore procedure in `docs/13-observability-and-ops.md`

### Deploy
- [ ] Run migrations against production
- [ ] Deploy the web application
- [ ] Smoke test on production: sign up, invite, sign in, switch to Arabic,
      check the audit log
- [ ] Open it on a phone browser and confirm it is usable

### Record
- [ ] Tag `v0.1.0`
- [ ] Write `plans/v0.1-platform-foundation/RELEASE.md`: what shipped, what was
      deliberately left out, anything that surprised you
- [ ] Update `docs/` wherever the build diverged from the specification
- [ ] Write `NOTES.md` — where you stopped and what v0.2 starts with

## Exit criteria

- [ ] Live on the production domain
- [ ] Smoke test passed against production
- [ ] Backup taken **and restored successfully**
- [ ] `v0.1.0` tagged and release notes written
- [ ] Documentation reconciled with what was actually built
