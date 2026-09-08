# 00 — Product Overview

## 1. What Sanad is

A multi-tenant B2B SaaS platform for **field service management (FSM)**. A company
subscribes, sets up its branches, staff, customers and equipment, then runs its
daily field operations through Sanad:

- Office staff schedule and dispatch work to engineers.
- Engineers receive work on a mobile app that works without connectivity.
- Engineers complete forms the company's own admin designed — inspections,
  service reports, safety checklists — capturing photos, signatures and GPS.
- Parts consumed on the job are deducted from the engineer's van stock.
- Managers see what happened, what it cost, and whether SLAs were met.

## 2. Category and competitive position

Sanad sits in the FSM category alongside ServiceTitan, Salesforce Field Service,
Jobber, Odoo Field Service, and Fieldwire, with a dynamic-forms capability
comparable to SafetyCulture (iAuditor).

**Where Sanad wins:**

| Advantage | Why it matters |
|---|---|
| Genuine Arabic and RTL support | Incumbents ship machine-translated Arabic with broken RTL layouts, if they ship it at all |
| MENA working calendars | Fri–Sat weekend, Hijri dates, Ramadan hours, Eid holidays — incumbents get these wrong |
| Local payment + e-invoicing | ZATCA / ETA compliance is a legal blocker competitors do not solve locally |
| WhatsApp as a first-class channel | The dominant business communication channel in the region |
| Form builder + FSM in one product | Competitors force customers to buy SafetyCulture *and* an FSM tool |
| Regional pricing | Global incumbents price in USD at levels that exclude most regional SMEs |

**Where we will not compete initially:** complex financial accounting, HR/payroll
as a system of record, heavy manufacturing/MRP, and fleet telematics hardware.
We integrate with those rather than rebuild them.

## 3. Target customer

**Primary:** companies of 10–500 staff with a mobile workforce in the Gulf and
Egypt — HVAC and MEP contractors, elevator maintenance, facilities management,
medical equipment servicing, IT field support, solar installation and O&M,
security systems, telecoms infrastructure.

**Buyer:** Operations Director or General Manager. Feels the pain as: work orders
lost in WhatsApp groups, paper job sheets that never come back, no idea what is
in the vans, and no evidence when a client disputes that work was done.

**Champion:** the dispatcher or operations coordinator — they live in the product
all day, so their screen determines whether the deployment survives.

## 4. Personas

| Persona | Role in system | Primary surface | Key need |
|---|---|---|---|
| **Platform Owner** | Runs Sanad itself | Admin console | Create/suspend tenants, plans, support, cross-tenant metrics |
| **Company Owner** | Tenant's first user | Web | Set up the company, invite staff, own billing |
| **Company Admin** | Configures the tenant | Web | Build forms, define job types, manage catalog and roles |
| **Dispatcher** | Assigns work | Web (dispatch board) | See everyone's day, fill gaps, react to emergencies |
| **Supervisor** | Reviews and approves | Web + mobile | Verify completed work, approve forms, handle exceptions |
| **Field Engineer** | Does the work | **Mobile, offline** | Today's jobs, navigate, fill forms fast, use van parts |
| **Inventory Manager** | Owns stock | Web + mobile scan | Accurate stock, replenish vans, run cycle counts |
| **Customer Contact** | Tenant's client | Portal (Phase 3) | Job status, sign-off, service history |

## 5. Core value loop

```
Customer request  →  Job created  →  Scheduled & dispatched  →  Engineer executes
       ↑                                                              │
       │                                                              ▼
   Invoice  ←  Cost (labour + parts)  ←  Verified  ←  Form + signature + photos
```

Every module exists to serve this loop. A feature that does not shorten,
de-risk, or add evidence to this loop is out of scope for v1.

## 6. Scope for v1 (all four pillars, per decision ADR-004)

**In scope**
- Multi-tenant platform with branches, RBAC, audit log
- Customers → Sites → **Assets** (equipment register with service history)
- Jobs / work orders, dispatch calendar, scheduling, recurring preventive maintenance, SLA timers
- Offline-first Expo mobile app for engineers
- Dynamic form builder with versioning, conditional logic, scoring, branded PDF export
- Inventory as a movement ledger with warehouses and van stock, serial/batch tracking
- Meetings, availability, agendas, minutes, action items, ICS feed
- Notifications across in-app, email, SMS and WhatsApp
- **English default, with full first-class Arabic including RTL** (ADR-014);
  per-tenant timezone, currency, weekend and holiday calendars
- Billing with regional gateways, subscriptions and seat management
- Platform admin console
- Reporting and dashboards for the core loop
- **Read-only customer portal** — job status, work report PDFs, asset history,
  contract visit entitlement (ADR-013)

**Explicitly out of scope for v1** (documented so it stays out)
- Customer portal beyond read-only: approvals, quote acceptance, self-service
  booking, messaging → Phase 3
- Quotes and invoicing to end customers → Phase 3
- Route optimization beyond proximity sorting → Phase 4
- Two-way Google/Outlook calendar sync → Phase 3
- SSO/SAML, white-labelling, custom roles → Phase 4 (enterprise)
- AI features → Phase 4
- Accounting integrations → Phase 3
- Any locale beyond English and Arabic — removed from the roadmap entirely, not
  deferred (ADR-014)

## 7. The asset register — why it is in v1

Not in the original request, but it is the spine that makes the other four
pillars coherent. Without it, jobs, forms and parts are disconnected activity
logs. With it:

- Every form submission, part consumed, and hour worked attaches to a specific
  piece of equipment at a specific site.
- Service history per asset becomes queryable — the basis for warranty claims,
  contract renewals, and later predictive maintenance.
- A QR sticker on a chiller opens its history and the right form on a phone.
- It is the single hardest dataset for a customer to migrate away from, which is
  the strongest retention mechanic in this category.

Cost to include now: roughly two tables and one screen. Cost to retrofit later:
a backfill across every historical job, form and stock movement.

## 8. Success criteria

**Product (pilot, first 3 tenants)**
- ≥ 80% of dispatched jobs completed in the mobile app rather than on paper
- ≥ 90% of completed jobs carry a submitted form with at least one photo
- Median engineer time to complete a standard form ≤ 4 minutes
- Zero data-loss incidents from offline sync

**Business (12 months post-launch)**
- 25 paying tenants, ≥ 400 billable seats
- Net revenue retention ≥ 105%
- Logo churn < 3% monthly
- CAC payback < 12 months

**Engineering**
- 99.9% uptime; p95 API latency < 300 ms
- Zero cross-tenant data-access incidents (isolation suite green on every commit)
- Change failure rate < 15%; median lead time to production < 2 days
