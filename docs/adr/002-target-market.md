# ADR-002 — MENA / Gulf market, Arabic-first

**Status:** Accepted · 2026-09-08 · Refined by ADR-009 (country choice) ·
**Partially corrected by ADR-011** (the field workforce in the Gulf is largely
not Arabic-speaking)

## Context
The target market determines language, payment gateways, tax and e-invoicing
obligations, notification channels, calendars and pricing.

## Decision
Build for the MENA / Gulf market with Arabic as the primary language and design
baseline, English at full parity.

## Consequences
- RTL is a first-class design constraint, not a translation layer. The dispatch
  board timeline, charts and drag interactions all need RTL treatment.
- Fri–Sat weekends, Hijri dates, Ramadan hours and Eid holidays are core
  scheduling inputs (`11-notifications-and-i18n.md` §5).
- WhatsApp is a primary notification channel, requiring a BSP and pre-approved
  message templates with multi-week lead time.
- Local gateways (mada, KNET, Benefit, Fawry) and bank-transfer invoicing are
  required; card-only billing is insufficient.
- ZATCA and ETA e-invoicing are legal obligations, handled through a certified
  provider.
- Pricing is set in local currency per market, not converted from USD.
- Data residency expectations in the Gulf require an enterprise deployment path
  (ADR-003).
