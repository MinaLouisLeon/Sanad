# v0.1 · Phase 1 — Decide

Settle everything that is expensive to change later. Nothing here produces
running code; all of it produces written decisions.

## Inputs

- `docs/01-architecture.md`, `docs/02-tenancy-and-rbac.md`, `docs/03-data-model.md`
- ADR-003 (hosting), ADR-006 (isolation), ADR-014 (languages), ADR-015 (delivery)

## Tasks

### Accounts and infrastructure
- [ ] Create the Supabase project; record the region (`eu-central-1`, ADR-003)
- [ ] Create the Vercel project and connect the repository
- [ ] Decide the environment set: local, preview per PR, production
- [ ] Decide where secrets live and how they are injected; no secrets in git
- [ ] Choose the domain and subdomain layout (`app.` for the tenant application)

### Database
- [ ] Write the **complete DDL** for every table in `docs/03-data-model.md`,
      not only the ones v0.1 uses. Modelling full now avoids reshaping populated
      tables later
- [ ] Decide the id strategy: UUIDv7, client-generatable (required by v0.4)
- [ ] Fix the RLS policy pattern and write it once, to be applied identically to
      every tenant-scoped table
- [ ] Decide the database roles: a migration role with DDL rights, a runtime role
      that **cannot bypass RLS**
- [ ] Decide how tenant context is set per transaction (`SET LOCAL app.tenant_id`)
- [ ] Decide the soft-delete convention and which tables use it
- [ ] Decide the audit log shape and which operations are always audited

### Identity and access
- [ ] Decide the auth methods for v0.1 — email and password only; phone OTP deferred
- [ ] Write the **permission catalogue**: every `module:resource:action` string
- [ ] Map the four roles to permission sets
- [ ] Decide scope qualifiers (`all`, `branch`, `own`) and which apply now
- [ ] Decide the invitation flow: token lifetime, single use, who may invite whom
- [ ] Decide session lifetimes for web; mobile values are set in v0.4

### Localization
- [ ] Decide the i18n library and the message key naming convention
- [ ] Decide catalog file structure and where `en` and `ar` live
- [ ] Pick the Arabic and Latin fonts and confirm the weights needed
- [ ] Decide the RTL approach: logical properties only, `dir` at the root
- [ ] Decide how the `en`/`ar` parity check runs in CI

### Repository
- [ ] Fix the monorepo layout against `docs/01-architecture.md` §4
- [ ] Decide package boundaries and the rule that `packages/core` imports no framework
- [ ] Decide linting, formatting and commit conventions
- [ ] Decide the migration tool and how migrations run in CI and production

## Decisions to record as ADRs

- [ ] Auth provider and session model
- [ ] Migration tooling and the expand/contract policy
- [ ] Any deviation from the specification set, with its reason

## Exit criteria

- [ ] Full DDL written and reviewed, covering every module
- [ ] RLS policy pattern written and understood well enough to apply by rote
- [ ] Permission catalogue complete
- [ ] Accounts created, secrets stored, domain registered
- [ ] New ADRs committed
