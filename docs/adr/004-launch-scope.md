# ADR-004 — All four pillars at launch

**Status:** Accepted · 2026-09-08

## Context
The four requested pillars are dynamic forms, field task management, inventory
and meetings. A narrower launch (forms + field tasks) was recommended as a
lower-risk wedge, with inventory and meetings following as expansions.

## Decision
Ship all four pillars in the first general release. Decision taken by the
product owner with the timeline trade-off understood.

## Consequences
- Estimated eight months to general availability with four engineers, versus
  roughly five for the narrower scope.
- Integration risk is managed by ending every milestone in demonstrable software
  and by starting a **pilot in month five**, before general availability.
- A cut-line order is agreed in advance (`15-roadmap.md` §5) so that scope
  reductions under schedule pressure are pre-decided rather than improvised.
- Tenant isolation, offline sync correctness, form version immutability, the
  audit log, backups and Arabic RTL quality are never cut.
- A larger surface means a longer feedback delay: the pilot is the primary
  mitigation and must not slip.
