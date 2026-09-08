# 10 — Billing, Plans & Monetization

## 1. Pricing model

**Per-seat monthly or annual, with tiered plans and metered overages.**

The critical structural decision in FSM pricing is distinguishing **field seats**
from **office seats**. Field engineers are the largest user group; charging them
the same as a dispatcher makes the product unaffordable for a 60-engineer
contractor and kills the deal at the exact size where it is most valuable.

| Seat type | Access | Relative price |
|---|---|---|
| **Field seat** | Mobile app, own jobs, forms, van stock | 1× |
| **Office seat** | Full web app, dispatch, admin, reporting | 2.5× |
| **Viewer** | Read-only reporting | Free, capped |

### Plan tiers

Prices below are the **validated hypothesis** to test with design partners during
M1–M2 (Q14), not placeholders.

| | **Starter** | **Professional** | **Enterprise** |
|---|---|---|---|
| **Monthly (AED)** | **399** | **1,299** | **from 3,500** |
| **Included seats** | 5 field + 2 office | 15 field + 5 office | Custom |
| **Extra field seat** | AED 55 | AED 45 | Negotiated |
| **Extra office seat** | AED 139 | AED 119 | Negotiated |
| **Annual** | 2 months free | 2 months free | Negotiated |
| Target | 5–20 staff | 20–150 staff | 150+ |
| Jobs & dispatch | ✓ | ✓ | ✓ |
| Dynamic forms | 10 templates | Unlimited | Unlimited |
| Inventory | Warehouse only | + Van stock, serials, batches | + Procurement |
| Meetings | ✓ | ✓ | ✓ |
| SLA management | — | ✓ | ✓ + custom policies |
| Recurring/PM | Basic | ✓ | ✓ |
| Branches | 1 | 5 | Unlimited |
| API & webhooks | — | ✓ | ✓ |
| Custom roles | — | — | ✓ |
| SSO | — | — | ✓ |
| White-label | — | — | ✓ |
| Data residency | Shared cloud | Shared cloud | Dedicated / in-country |
| Support | Email | Priority | Named CSM, SLA |

Metered above plan allowances: form submissions, storage (photos dominate),
SMS and WhatsApp messages, API calls. Overages are billed, never used to block
field work.

Sanity check: a 40-engineer contractor on Professional pays about AED 2,400 per
month — roughly **USD 16 per engineer per month**. Far below ServiceTitan-class
pricing, comfortably above cost to serve.

Saudi pricing is near-identical in SAR.

### Regional pricing reality

Price in **local currency** (SAR, AED, EGP), not USD. Egyptian pricing must be
set independently rather than converted — a USD-converted price is roughly 4×
what the market bears, and this single mistake loses the entire Egyptian segment.

## 2. Payment methods — B2B in MENA

Card-only billing is a common and costly mistake here. Mid-market and enterprise
customers in the Gulf and Egypt overwhelmingly pay **annually by bank transfer
against a proper tax invoice**, often after a procurement cycle.

Required from day one:

| Method | Notes |
|---|---|
| **Bank transfer / manual invoice** | Essential. Generate the invoice, mark paid on reconciliation, activate the subscription |
| **mada** (KSA) | Domestic debit scheme; far higher acceptance than international cards |
| **KNET** (Kuwait), **Benefit** (Bahrain) | Domestic schemes |
| Visa / Mastercard | Standard |
| Apple Pay | Strong adoption in the Gulf |
| **Fawry** (Egypt) | Cash and agent payments; materially expands reach |

**Gateways:** Tap, HyperPay or Moyasar for the Gulf; Paymob or Fawry for Egypt;
Stripe for international customers.

**Design consequence:** a `PaymentProvider` interface in the billing module with
per-provider adapters, selected by tenant country. Never let gateway specifics
leak into the domain layer — the first gateway will be replaced.

## 3. Subscription lifecycle

```
trial (14 days, no card) → active → past_due → suspended → cancelled
                              ↑         │
                              └─payment─┘
```

- Trials require no card. Requiring one in this market halves signups.
- Seat changes prorate; adding a seat mid-cycle is immediate, removals apply at
  renewal.
- Dunning: reminders at day 1, 3, 7 and 14 past due across email, WhatsApp and
  in-app.
- **Grace behaviour is deliberate:** at `past_due` the web app goes read-only,
  but the **mobile app continues to sync completed work upward**. A customer's
  field data must never be held hostage over an unpaid invoice — the reputational
  cost is unrecoverable and the legal position is poor.
- Cancellation retains data for 30 days with export available, then deletion.

## 4. Seat management

- Seats are purchased, then assigned to users.
- Assigning a user beyond the purchased count prompts an upgrade rather than
  silently overcharging.
- Deactivating a user frees a seat immediately.
- Seasonal contractors are common in the region — support monthly seat reduction
  without a full plan downgrade.

## 5. Tax

- VAT: 15% KSA, 5% UAE, 14% Egypt, 10% Bahrain, 5% Oman. Rates change; store
  them as dated configuration, never as constants.
- B2B reverse charge for cross-border GCC supplies.
- Tenant tax registration number captured at signup and printed on every invoice.
- Invoices must carry both Arabic and English content to be locally acceptable.

## 6. E-invoicing — deferred out of v1 (ADR-009)

**Decision: no e-invoicing integration ships in v1.** Under ADR-009 the company
is UAE-registered, where e-invoicing is not yet mandatory at our scale, and
subscriptions sold to VAT-registered Saudi businesses are a cross-border B2B
supply settled by the customer under **reverse charge** — so no ZATCA obligation
attaches to our own subscription invoices.

**This reading must be confirmed with a UAE/KSA tax advisor before the first
invoice is issued.** It removes an entire workstream from the launch, which makes
it the highest-value item to verify early rather than assume.

Revisit when any of these becomes true: the UAE mandate reaches our size of
business; a Saudi entity is registered; or tenant-to-customer invoicing ships in
Phase 3, at which point tenants need compliant output. The
`invoices.einvoice_*` fields remain in the data model so this is a feature
addition, never a migration.

The reference material below applies **when that day comes**.

### Reference: what compliance will require

**Saudi Arabia (ZATCA / Fatoora, Phase 2)** requires invoices as UBL 2.1 XML,
cryptographically stamped with a certificate obtained through onboarding, a
QR code with prescribed TLV fields, and clearance (B2B) or reporting (B2C)
through ZATCA's API. **Egypt (ETA)** imposes a comparable regime with its own
schema, signing requirements and submission API.

**When it becomes necessary, do not build this in-house.** Use a certified provider or
middleware for both markets. In-house ZATCA Phase 2 compliance is a multi-month
project with certification, cryptographic hardware or key custody concerns, and
ongoing regulatory change — entirely disproportionate to the volume of invoices
Sanad itself issues.

Applies in two directions, and it is worth being explicit about which:
1. **Sanad → tenants** (our own subscription invoices) — required from the first
   Saudi or Egyptian customer.
2. **Tenants → their customers** — only relevant once we ship the Phase 3
   invoicing module. Deferred, but the data model reserves `invoices.einvoice_*`
   fields now so it is not a migration later.

## 7. Revenue operations

- Metrics tracked from launch: MRR, ARR, ARPA, net revenue retention, logo churn,
  seat expansion, trial-to-paid conversion, CAC payback, activation rate.
- **Activation is the leading indicator** for this product: a tenant that has
  completed 10 jobs with submitted forms in the mobile app within 14 days
  converts and retains dramatically better than one that has only logged in.
  Instrument it from day one and drive onboarding against it.
- Provider webhooks are the source of truth for payment state, verified by
  signature, processed idempotently, and reconciled by a nightly job that
  compares provider state against local subscription state and alerts on drift.

## 8. Partner and reseller channel

Worth designing for early: regional software sales run heavily through local
integrators and IT resellers, and channel deals are frequently how a Gulf
enterprise buys at all.

Minimum viable version (Phase 3): a partner account that can create and manage
multiple tenants, see their subscription status, and receive a commission
report. The tenancy model already supports this — a partner is an entity with
a managed relationship to several tenants — provided nothing hard-codes the
assumption that a tenant is owned only by its own users.
