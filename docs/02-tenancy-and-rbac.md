# 02 — Tenancy, Identity & Access Control

## 1. The hierarchy

```
Platform  (Sanad itself)
└── Tenant / Company                 ← subscription + data isolation boundary
    └── Branch                       ← operational scope (city, region, division)
        └── Team                     ← dispatch grouping (e.g. "HVAC crew A")
            └── User (membership)
```

Branches exist from day one even for single-branch customers, where the UI hides
branch selection behind a default. Retrofitting a scoping level after data exists
requires touching every table, every query, every permission check and every
historical row. The cost now is one column and one index.

## 2. Two separate super-admin concepts

| | **Platform Owner** | **Company Owner** |
|---|---|---|
| Who | Sanad staff | The tenant's first user |
| App | `admin.sanad.app` (separate deployment) | `app.sanad.app` |
| Auth | Separate user table, mandatory MFA, IP allowlist | Standard tenant auth |
| Can | Create/suspend tenants, set plans and feature flags, view cross-tenant metrics, impersonate with consent | Manage everything inside their own tenant only |
| Cannot | Read tenant business data without an audited impersonation session | See any other tenant |

These are **different identity systems that never merge**. A single account able
to be both is the most common root cause of catastrophic B2B breaches. The admin
console runs as a separate Vercel project on a separate domain with its own
session cookie scope.

### Impersonation ("support access")

Support genuinely needs it, so build it correctly rather than leaving people to
share passwords:

- Requires a stated reason and links to a support ticket reference.
- Time-boxed: expires automatically after 60 minutes.
- **Read-only by default**; write access needs a second Platform Owner's approval.
- Tenant owners see a permanent log of every impersonation session.
- A persistent banner shows in-app: "Sanad support is viewing this account."
- Every action carries `acting_user_id` *and* `impersonated_by` in the audit log.
- Tenants on enterprise plans can require prior consent per session.

## 3. Isolation strategy

**Shared database, shared schema, `tenant_id` on every tenant-scoped table,
enforced by Postgres Row-Level Security.**

Rejected alternatives:
- *Schema per tenant* — migrations across thousands of schemas become an
  operational hazard; connection pooling degrades.
- *Database per tenant* — correct for enterprise/on-prem, wasteful at SME price
  points. Kept available as a paid tier (see `01-architecture.md` §8).

### How RLS is applied

Every tenant-scoped table carries `tenant_id uuid not null references tenants(id)`,
has RLS enabled and forced, and has a policy of the shape:

```sql
alter table jobs enable row level security;
alter table jobs force row level security;

create policy tenant_isolation on jobs
  using      (tenant_id = current_setting('app.tenant_id', true)::uuid)
  with check (tenant_id = current_setting('app.tenant_id', true)::uuid);
```

Rules:
- `FORCE ROW LEVEL SECURITY` so the table owner is not exempt.
- The application connects as a **non-superuser, non-owner role** that cannot
  bypass RLS.
- Migrations run as a separate privileged role, never the runtime role.
- `current_setting(..., true)` returns null when unset, and `null = uuid` is
  null → no rows. A missing tenant context fails closed.
- A CI check fails the build if any new table lacks `tenant_id` + an RLS policy.
- Branch scoping is layered as a **second policy** using `app.branch_ids`, so a
  dispatcher limited to Jeddah cannot read Riyadh jobs.

### Proving it works

`14-testing-and-quality.md` defines a mandatory isolation suite: for every
tenant-scoped table and every API endpoint, an automated test seeds two tenants
and asserts that tenant A receives exactly zero of tenant B's rows, including
through search, exports, aggregate counts, file URLs and error messages. It runs
on every commit. A new endpoint without isolation coverage fails CI.

## 4. Identity

- **Users are global**; membership binds a user to a tenant. One email can belong
  to several tenants (contractors serving multiple companies) and picks a tenant
  at login. This is important in the region, where subcontracting is common, and
  is painful to add later.
- Authentication: email + password (Argon2id), email OTP, and — given the market —
  **phone + OTP**, which many field engineers strongly prefer over passwords.
- MFA (TOTP) available to all; enforceable by tenant policy; mandatory for
  Company Owners on paid plans.
- Sessions: short-lived access token (15 min) plus rotating refresh token.
  **Mobile refresh tokens live 90 days** — an engineer must not be logged out
  mid-shift in a basement. Device-bound, individually revocable, listed in a
  "your devices" screen.
- Invitations: single-use, expiring tokens; the inviter's permissions cap the
  invitee's role (nobody can invite someone more privileged than themselves).
- Account lifecycle: invited → active → suspended → offboarded. Offboarding
  preserves historical attribution — a departed engineer's completed forms and
  signatures must never be deleted or reattributed.

## 5. Roles and permissions

Permissions are **capability strings grouped into roles**, never `if (role === 'admin')`
scattered through the codebase.

Format: `<module>:<resource>:<action>` — e.g. `field_ops:job:assign`,
`forms:template:publish`, `inventory:movement:adjust`.

### System roles shipped by default

| Role | Scope | Summary |
|---|---|---|
| `owner` | Tenant | Everything, including billing and tenant deletion |
| `admin` | Tenant | Everything except billing and tenant deletion |
| `dispatcher` | Branch | Create/schedule/assign jobs, view all engineers' calendars |
| `supervisor` | Branch/Team | Review and approve work, verify forms, override statuses |
| `engineer` | Self | Own assigned jobs, submit forms, consume van stock |
| `inventory_manager` | Branch | Full stock control, transfers, counts, procurement |
| `viewer` | Branch | Read-only reporting access |

Custom roles are Phase 4, but the data model supports them now: a role is a row
with a permission array, and system roles are simply rows flagged immutable.

### Scope qualifiers

A permission grant carries a scope, which is what makes branch structure useful:

- `all` — anywhere in the tenant
- `branch` — the branches on the user's membership
- `team` — the user's teams
- `own` — records where the user is assignee or creator

So `field_ops:job:read@branch` and `field_ops:job:update@own` are distinct grants.
Every service-layer read applies the scope filter; every write re-checks it
server-side. Client-side permission checks exist only to hide UI — they are never
trusted.

### Field-level and record-level rules

Some rules cannot be expressed as capabilities and belong in domain logic:
- An engineer may edit a submitted form only until a supervisor verifies it.
- Cost prices are hidden from `engineer` even on records they can read.
- A job in `closed` state is immutable except by `admin` with a stated reason,
  which is audited.

## 6. Tenant lifecycle

| Stage | Behaviour |
|---|---|
| **Signup** | Company name, country, timezone, industry → tenant + owner created, 14-day trial |
| **Onboarding** | Guided wizard: branches → staff invites → first customer/site/asset → first form (from template library) → first job. Optional demo dataset, one-click removable. |
| **Trial → paid** | Seat count confirmed, gateway selected, subscription activated |
| **Past due** | Grace period, then read-only mode. **Never block the mobile app's ability to sync completed work upward** — losing a customer's field data over an unpaid invoice is unforgivable |
| **Suspended** | Login blocked, data retained, exports still available |
| **Export** | Self-service full export: JSON/CSV plus original files and rendered form PDFs |
| **Deletion** | 30-day soft delete → hard delete with a certificate of destruction. Anonymize rather than delete where records are legally required |

## 7. Feature flags and entitlements

Two distinct mechanisms, often conflated:

- **Entitlements** — what a plan includes (`inventory.serial_tracking`,
  `forms.max_templates: 50`). Enforced server-side in the domain layer, checked
  on write, not just hidden in UI.
- **Feature flags** — rollout control for unfinished work, per tenant or per
  user, owned by engineering.

Both are read through one interface so a screen never needs to know which is
which.
