# ADR-009 — UAE entity and launch market, Saudi as primary revenue target

**Status:** Accepted · 2026-09-08 · Refines ADR-002

## Context
ADR-002 committed to the MENA/Gulf market without choosing a country. Payment
gateways, e-invoicing obligations, VAT, data residency expectations, pricing and
sales motion all differ materially between Saudi Arabia, the UAE and Egypt, and
building for all three simultaneously dilutes all three.

## Decision
- Register the company in a **UAE free zone**.
- Sell to **UAE customers first**; Dubai, Sharjah and Abu Dhabi.
- Enter **Saudi Arabia from month 6–9**, the primary long-term revenue market.
- **Egypt is wave three**, priced independently in EGP.

## Rationale
The UAE offers remote company formation in days, straightforward corporate
banking, immediate payment-gateway eligibility, and — decisively at this stage —
enough customer density that a small team can visit three sites in a morning.
Proximity to users outweighs market size while the product is being designed.

Saudi Arabia is the larger market with the highest willingness to pay, but it
imposes ZATCA Phase 2 e-invoicing, expects local presence, and runs longer
procurement cycles. It is the right second market, not the right first one.

Egypt's willingness to pay is 4–6× lower and EGP devaluation risk makes revenue
planning unreliable.

## Consequences
- **The e-invoicing workstream leaves v1** (ADR supersedes the v1 portion of
  `10-billing-and-plans.md` §6). A UAE entity supplying VAT-registered Saudi
  businesses is a cross-border B2B supply settled by reverse charge, so no ZATCA
  obligation attaches to our own subscription invoices; UAE e-invoicing is not
  yet mandatory for our size. **To be confirmed with a regional tax advisor
  before the first invoice is issued.**
- Payment gateway is Tap Payments, covering UAE cards, Saudi mada and Kuwaiti
  KNET in one integration (Q12).
- Pricing is set in AED with near-identical SAR figures; EGP is set
  independently, never converted.
- Support hours follow Sunday–Thursday Gulf Standard Time.
- Entering Saudi later may require a local entity or a channel partner; the
  partner/reseller model in `10-billing-and-plans.md` §8 becomes relevant sooner
  than expected.
