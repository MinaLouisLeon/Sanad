# v0.1 · Phase 3 — Verify

The isolation suite built here is the most important test asset in the project.
Every later version extends it. A gap here is not recoverable later.

## Tasks

### Tenant isolation suite — blocking
- [ ] Harness that seeds two tenants and authenticates as a user of each
- [ ] For every tenant-scoped table: assert tenant A reads **exactly zero** of
      tenant B's rows
- [ ] Same assertion through every route: direct fetch by B's id, lists, search,
      counts, exports
- [ ] Assert a write referencing B's id fails
- [ ] Assert a missing record and a forbidden record are indistinguishable —
      404, never a 403 that confirms existence
- [ ] Assert a request with no tenant context returns nothing rather than everything
- [ ] Wire the suite into CI as a blocking check
- [ ] Add the coverage check: a route or table without isolation coverage fails CI

### Authentication and authorization
- [ ] Sign up, sign in, sign out, password reset, email verification
- [ ] Invitation: accept, expiry, reuse rejected, privilege escalation rejected
- [ ] Session expiry and refresh
- [ ] Each role against each permission, allowed and denied
- [ ] Server-side denial holds when the UI check is bypassed

### Localization
- [ ] Automated parity check: every `en` key exists in `ar`
- [ ] Manual pass over every screen in Arabic RTL — layout, alignment, icon
      mirroring, form fields, tables, menus
- [ ] Dates, numbers and currency correct in both locales
- [ ] Long Arabic strings do not break layouts

### Audit log
- [ ] Every audited action produces a row with correct before and after
- [ ] The runtime role cannot update or delete audit rows

### Manual QA script
- [ ] Write `plans/v0.1-platform-foundation/QA.md` as a repeatable checklist,
      to be re-run before every release from here on

## Exit criteria

- [ ] Isolation suite green and blocking in CI
- [ ] All auth and RBAC tests passing
- [ ] `en`/`ar` parity check green
- [ ] Every screen reviewed in Arabic RTL
- [ ] Manual QA script written and executed once
