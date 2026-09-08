# v0.1 · Phase 2 — Build

## Inputs

Phase 1 complete: DDL written, RLS pattern fixed, permission catalogue agreed.

## Tasks

### Repository scaffold
- [ ] Turborepo + pnpm workspace
- [ ] `apps/web` — Next.js App Router, TypeScript, Tailwind
- [ ] `packages/db` — schema, migrations, RLS policies, seeds
- [ ] `packages/core` — domain logic, framework-free
- [ ] `packages/ui` — design system primitives
- [ ] `packages/i18n` — catalogs and formatters
- [ ] `packages/config` — shared ESLint, TypeScript, Tailwind presets
- [ ] CI: typecheck, lint, test, build on every push

### Database
- [ ] Migrations creating every table from the full DDL
- [ ] `ENABLE` and `FORCE ROW LEVEL SECURITY` on every tenant-scoped table
- [ ] Tenant isolation policy applied to every one of them
- [ ] Indexes leading with `tenant_id`
- [ ] Runtime role created with no RLS bypass; migration role separate
- [ ] A **CI check that fails the build** on any table lacking `tenant_id` or a policy
- [ ] Seed script creating two tenants with distinct data, for isolation testing

### Tenant context
- [ ] A single helper that opens a transaction and sets `app.tenant_id` and
      `app.user_id` — the only sanctioned way to reach the database
- [ ] Domain services take no `tenantId` argument; context comes from the session
- [ ] Background jobs must set context explicitly and fail loudly without it

### Authentication
- [ ] Sign up: creates a tenant, its owner, and a membership
- [ ] Sign in, sign out, password reset, email verification
- [ ] Custom access-token claims carrying `tenant_id`, `role`, `branch_ids`
- [ ] Session handling in the App Router; middleware resolving tenant context
- [ ] Invitation: create, send email, accept, expire

### Authorization
- [ ] Permission catalogue in code, as constants
- [ ] The four roles seeded as system roles
- [ ] A server-side `can()` check used by every service method
- [ ] UI permission helpers for hiding controls — never the only check

### Audit log
- [ ] Append-only table; runtime role granted `INSERT` and `SELECT` only
- [ ] A writer that records actor, action, entity, before, after, IP, request id
- [ ] Wired into authentication events, role changes and membership changes
- [ ] An audit log screen for tenant admins, filterable

### Localization
- [ ] i18n configured with `en` default and `ar`, locale in the route
- [ ] Both catalogs, with a language switcher
- [ ] Fonts loaded for Latin and Arabic
- [ ] `dir` set from the locale; logical properties throughout
- [ ] Number, date and currency formatters per locale
- [ ] Tenant settings: timezone, weekend days, default locale

### Application shell
- [ ] App layout: navigation, header, user menu, tenant switcher
- [ ] Screens: sign in, sign up, accept invitation, dashboard placeholder,
      users list, invite user, tenant settings, profile, audit log
- [ ] Loading, empty and error states as shared components from the start

## Exit criteria

- [ ] Sign up, invite, accept and sign in all work
- [ ] A user's role visibly restricts what they can see and do
- [ ] Language switching works and Arabic renders RTL correctly
- [ ] Every write lands in the audit log
- [ ] Seeds produce two tenants with separate data
