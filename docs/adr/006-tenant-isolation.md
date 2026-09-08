# ADR-006 — Shared database with Row-Level Security

**Status:** Accepted · 2026-09-08

## Context
Cross-tenant data leakage is the existential risk for a B2B SaaS platform.
Isolation can be enforced by application code, by schema, or by database.

## Options
- **Application-layer filtering only** — one forgotten `WHERE` clause leaks
  another company's data. Rejected.
- **Schema per tenant** — migrations across thousands of schemas become an
  operational hazard; connection pooling degrades. Rejected for the shared cloud.
- **Database per tenant** — correct for enterprise and regulated customers,
  wasteful at SME price points. Retained as a paid tier.
- **Shared schema with `tenant_id` and forced RLS** — chosen.

## Decision
Shared Postgres, `tenant_id` on every tenant-scoped table, `ENABLE` and `FORCE
ROW LEVEL SECURITY`, policies reading `current_setting('app.tenant_id')`, and an
application database role that cannot bypass RLS. Branch scoping is layered as a
second policy.

## Consequences
- A forgotten tenant filter returns zero rows instead of another tenant's data.
- Tenant context must be set on every connection, including in background jobs;
  a job without context fails closed.
- Migrations run under a separate privileged role.
- A CI check fails the build for any new table lacking `tenant_id` and a policy.
- The isolation test suite (`14-testing-and-quality.md` §2) is a blocking gate on
  every commit.
