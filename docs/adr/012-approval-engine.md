# ADR-012 — One small generic approval engine

**Status:** Accepted · 2026-09-08

## Context
At least six flows need approval: form verification, stock adjustments, stock
count variance, purchase orders, time-off requests and reopening a closed job.
Each could be implemented independently.

## Decision
Build a single approvals mechanism in M1: an `approvals` table plus a policy per
approvable type defining what triggers approval, any value threshold, the
approver role and scope, escalation, and timeout behaviour.

**Explicitly excluded from v1:** visual workflow designer, branching graphs,
parallel approval chains, delegation rules. These turn a two-week component into
a quarter-long product.

## Consequences
- Roughly two weeks in M1 versus five or six divergent half-implementations.
- One audit trail shape, one notification integration, one approvals inbox.
- Adding an approvable type later is configuration, not new plumbing.
- The exclusions are a real constraint: a tenant needing two-stage or parallel
  approval cannot be served in v1, and that is accepted.
