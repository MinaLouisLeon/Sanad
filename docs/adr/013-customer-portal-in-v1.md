# ADR-013 — A read-only customer portal ships in v1

**Status:** Accepted · 2026-09-08 · Supersedes the Phase 3 deferral in `00-product-overview.md` §6

## Context
The customer portal was deferred to Phase 3 as a scope-control measure, while
being identified as the strongest differentiator in the plan. Under ADR-010, the
first vertical sells annual maintenance contracts, where the customer's
facilities manager decides renewal.

## Decision
Ship a **read-only** customer portal in v1, built in M6.

**In scope:** job status and scheduled date, completed work reports as PDF, asset
service history, contract visit entitlement ("7 of 12 visits used"). Magic-link
authentication — no accounts, no passwords, no customer user management.

**Out of scope until Phase 3:** approvals, quote acceptance, self-service
booking, messaging, payment.

## Rationale
Renewal of an annual maintenance contract is decided by someone who never logs
into the tenant's system. Giving that person continuous evidence of work
performed is the renewal mechanic, and it makes Sanad visible to a population who
would otherwise never encounter it. It reuses data that already exists, so the
cost is presentation and authentication rather than new domain work — roughly
three weeks.

## Consequences
- It is a **new externally-facing attack surface** and must be explicitly in
  scope for the pre-launch penetration test.
- Magic-link tokens need short expiry, single use, and per-customer scoping
  enforced by the same RLS mechanism as everything else.
- It is placed at M6 and is **first on the cut line** (`15-roadmap.md` §5), so it
  absorbs schedule pressure rather than the modules it depends on.
- Tenants must be able to disable it per customer — not every client should see
  every record.
