# ADR-002 — MENA / Gulf market

**Status:** Accepted · 2026-09-08 · Refined by ADR-009 (country choice) ·
**Language policy superseded by ADR-014** — English is the default locale and
only English and Arabic are supported. The market decision below stands
unchanged; only the language ordering in it does not.

## Context
The target market determines language, payment gateways, tax and e-invoicing
obligations, notification channels, calendars and pricing.

## Decision
Build for the MENA / Gulf market, with full Arabic support including RTL.

*(Language ordering superseded by ADR-014: English is the default locale, Arabic
is a fully supported first-class locale, and no other locale ships.)*

## Consequences
- RTL is a first-class design constraint, not a translation layer. The dispatch
  board timeline, charts and drag interactions all need RTL treatment. ADR-014
  keeps this requirement while flipping the default locale, and lists the
  controls that stop RTL quality from rotting.
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
