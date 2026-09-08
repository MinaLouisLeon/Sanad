# ADR-010 — HVAC/MEP maintenance contractors as the first vertical

**Status:** Accepted · 2026-09-08

## Context
The product model fits many field-service verticals, but onboarding, the starter
template library, demo data, terminology and sales messaging can only be
optimized for one.

## Decision
Optimize for **HVAC/MEP maintenance contractors running annual maintenance
contracts**, with **fire & safety systems** shipped as an adjacent template set.

## Rationale
- Largest population of mid-size, highly fragmented companies in the Gulf, with
  no entrenched incumbent.
- The AMC model maps precisely onto the platform's contracts, preventive-
  maintenance recurrence and asset register — the product's structure is already
  the customer's commercial structure.
- Cooling is non-discretionary in this climate, so maintenance spend survives
  downturns.
- Fire & safety shares roughly 90% of the model and adds regulatory pull, since
  Civil Defence inspection requirements demand exactly the forms-plus-assets
  evidence trail the product produces.

Rejected: elevators (OEM-dominated, thin independent market), integrated FM
(existing CAFM systems, long procurement), medical equipment (small market,
hospital sales cycles), solar O&M (still small in absolute terms).

## Consequences
- The ten platform starter templates are authored for HVAC/MEP and fire & safety.
- Demo and seed data model a chiller-and-AHU estate.
- Terminology (`17-terminology-ar.md`) is validated against HVAC supervisors.
- Design partners are recruited from this vertical (`18-design-partner-program.md`).
- Other verticals remain fully supported; this decision governs defaults and
  messaging, not capability.
