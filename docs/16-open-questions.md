# 16 — Decisions on the Blocking Questions

Every question from the previous revision now carries a decision and its
reasoning. Items marked **NEEDS YOUR INPUT** depend on a fact only the product
owner holds; each states the assumption being used until it is confirmed.

Legal, tax and corporate-structure reasoning below is **not professional advice**
and must be confirmed with a regional advisor before money is spent.

> **Answered since this document was written.** The team is **one part-time
> developer, under 10 hours per week**, with no deadline and no customer waiting.
> Q1, Q2, Q9, Q12, Q13, Q14 and Q15 are revised in place below; ADR-015 records
> what that changes. Q3, Q4, Q5, Q6, Q7, Q8, Q10, Q11 and Q16–Q20 stand as
> written.

---

## Q1 — Team and budget → **ANSWERED: solo, part-time, under 10 hrs/week**

**Actual answer.** One developer — the product owner — working under 10 hours per
week with Claude assistance, no deadline, no external funding, and no customer
waiting. Budget is effectively zero beyond hosting.

This invalidates everything below, which assumed a funded team. See **ADR-015**
and the rewritten `15-roadmap.md` for the plan that replaces it. The headline:
four pillars are kept, the depth of each is cut, a walking skeleton through all
four lands around month 10, and the complete thin v1 lands around month 30–36 at
this pace.

The table below is retained only because it shows what additional hours buy.

<details>
<summary>Superseded: the funded-team plan</summary>

**Decision.** The 8-month plan assumes 4 engineers, 0.5 designer, 0.5 QA,
0.25 DevOps. Hire in Egypt or Jordan, with the lead wherever the founder is.
Arabic-native, timezone-aligned with Gulf customers, and roughly a third of UAE
salary cost.

**Timeline by team size — these are not interchangeable:**

| Engineers | Full four-pillar scope | Recommendation |
|---|---|---|
| 2 | 14–16 months | **Revisit ADR-004.** At this size, ship forms + field ops in 5–6 months and expand |
| 3 | ~11 months | Viable; cut lines will be used |
| **4** | **8 months** | **Planned** |
| 6 | ~6.5 months | Diminishing returns — sync and dispatch do not parallelize further |

**Indicative budget to GA, regionally staffed:**

| Item | 8 months |
|---|---|
| 4 engineers @ USD 3.5–5k/mo | 112–160k |
| Design 0.5 + QA 0.5 | 28–40k |
| Infrastructure and services | 6–12k |
| Penetration test | 8–15k |
| Legal entity, contracts, trademark | 8–15k |
| Design-partner incentives, template authoring | 3–6k |
| **Total** | **≈ 165–250k USD** |

UAE-based staffing roughly doubles this. A 2-engineer bootstrap on the narrowed
scope lands near 70–95k.

**Hiring order.** Recruit the **mobile/sync engineer first** — it is the longest
pole, the hardest role to fill, and the M0 spike depends on it. Then the tech
lead, then two full-stack.

</details>

---

## Q2 — Design partners → **ANSWERED: deferred, build first**

**Actual answer.** The product owner has chosen to build first and find users
later, twice. That is the decision and it is not revisited.

**Adaptation** (`15-roadmap.md` §2): the phases are ordered so the surfaces that
cannot be designed without watching real users — the dispatch board, scheduling
intelligence, SLA logic — are built **last**, in Phase C and beyond. The parts
that are identical in every field-service business come first. This makes
building without users as cheap as it can be.

`18-design-partner-program.md` is retained in full for when this changes.

<details>
<summary>Superseded: the recruit-first plan</summary>

**Decision.** Non-negotiable. Three committed partners, recruited during M0 while
foundations are built. Assume none exist today and run the plan in
`18-design-partner-program.md`.

Target profile: 15–60 field staff, HVAC/MEP or fire-and-safety AMC contractor in
Dubai, Sharjah or Abu Dhabi, currently running on paper job sheets, WhatsApp
groups and Excel.

The offer is co-design, not beta access: free for 18 months, their forms built
for them by us, a direct line to the team, in exchange for two hours a week of
the operations manager, **one full day shadowing a dispatcher**, and **two
engineer ride-alongs**. A one-page letter of intent, not a contract.

A prospect who will not give up a dispatcher day is not a design partner.
The dispatcher day and ride-alongs must happen **before M2 begins** — the
dispatch board cannot be designed from a specification.

</details>

---

## Q3 — Launch market → **UAE entity and first customers, Saudi as the revenue target**

**Decision.** Register in a UAE free zone. Sell into the UAE first. Enter Saudi
Arabia from month 6–9. Egypt is wave three.

**Reasoning:**

| | UAE | Saudi Arabia | Egypt |
|---|---|---|---|
| Market size | Medium, dense | **Largest** | Largest by count, smallest by value |
| Willingness to pay | High | **Highest** | 4–6× lower |
| Company setup | **Days, remote, free zone** | Slower, local presence expected | Straightforward but hurts Gulf credibility |
| Payment gateway access | **Easy** | Needs local CR for mada | Fawry/Paymob, low card penetration |
| E-invoicing burden | Phasing in from ~2026–27 | **ZATCA Phase 2, live and strict** | ETA, live and enforced |
| Sales cycle | **Short, visit-driven** | Long, relationship and procurement heavy | Medium |
| Currency risk | Pegged to USD | Pegged to USD | **EGP devaluation risk** |

The decisive factor is that Dubai and Sharjah let a small team drive to three
customer sites in a morning. At this stage, proximity to users beats market size.

**This deletes a workstream.** A UAE entity selling subscriptions to
VAT-registered Saudi businesses is a cross-border B2B supply, where the Saudi
customer self-accounts under reverse charge — so **no ZATCA e-invoicing
obligation on our own subscription invoices**. Combined with UAE e-invoicing not
yet being mandatory, the entire e-invoicing workstream leaves v1. See Q13.

**NEEDS YOUR INPUT:** where you are resident. A UAE free-zone licence can be set
up remotely from Egypt, but it changes cost and banking friction, and an
Egypt-registered entity selling into the Gulf faces real credibility and payment
friction.

---

## Q4 — First vertical → **HVAC / MEP maintenance contractors, with fire & safety adjacent**

**Decision.** Optimize onboarding, the template library, terminology and the
sales pitch for HVAC/MEP contractors running annual maintenance contracts. Ship
fire-and-safety templates alongside, because the model is ~90% identical and the
regulatory pull is stronger.

**Why not the others:**

| Vertical | Verdict |
|---|---|
| **HVAC / MEP** | **Chosen.** Largest count of mid-size firms in the Gulf, highly fragmented, no incumbent, AMC model maps exactly onto contracts + PM recurrence + asset register. Cooling is not optional in this climate, so maintenance spend is non-discretionary |
| Fire & safety | Close second, shipped as templates. Civil Defence inspection requirements create legal pull for exactly our forms-plus-assets model |
| Elevators | Regulated and asset-centric, but dominated by OEMs running their own systems; the independent market is thin |
| Integrated FM | Avoid at launch. They already run a CAFM system and want integrations, not replacement. Long procurement cycles |
| Medical equipment | High compliance value, but small market and hospital sales cycles measured in quarters |
| Solar O&M | Growing, still small in absolute terms |

Nothing prevents other verticals from using the product — this decision sets the
default templates, the demo data, and the words on the website.

---

## Q5 — Customer portal → **REVERSED: out of v1 entirely (ADR-015)**

A solo developer at this pace cannot carry a second externally-facing surface and
the penetration-test obligation attached to it. The product reasoning below is
unchanged and still correct — it remains the strongest differentiator identified
in this plan — but it is deferred until after v1 on capacity grounds.

<details>
<summary>Superseded: portal in v1</summary>

### Original decision — read-only, in v1

**Decision reversed from the previous revision.** A minimal read-only portal
ships in v1, built in M6, and it is the first item on the cut line.

Scope: job status and scheduled date, completed work reports as PDF, asset
service history, contract visit entitlement ("7 of 12 visits used"). Magic-link
authentication, no accounts, no passwords. Roughly three weeks.

Explicitly excluded until Phase 3: approvals, quote acceptance, self-service
booking, messaging.

**Why it moved.** In the AMC model the customer's facilities manager decides
renewal. Visibility into what was done, when, and by whom is the renewal
mechanic, and it makes Sanad visible to a population who never log into the
tenant's system. It reuses data that already exists.

**Risk accepted:** it is a new externally-facing attack surface and must be in
scope for the penetration test.

</details>

---

## Q6 — Multi-currency → **Model now, expose in Phase 3**

**Decision.** Currency lives on customer, contract, and every cost and price
record — not only on the tenant. Add a dated `fx_rates` table. Reporting rolls up
into the tenant's base currency at the rate on the transaction date.

A contractor operating in both Dubai and Riyadh is a near-term reality given Q3.
The cost now is one column and one small table; the retrofit cost is every
costing and reporting query in the product.

The UI stays single-currency in v1.

---

## Q7 — Arabic terminology → **Draft written, owned by the product owner, due end of M0**

**Decision.** A first draft of ~45 core terms is in
[`17-terminology-ar.md`](./17-terminology-ar.md). The product owner owns it; a
native-Arabic operations person from a design partner reviews it — a translator
will not get "dispatch" or "van stock" right in a maintenance context.

Frozen before the first user-facing screen in M1. Changing the Arabic word for
"job" after 200 screens exist is a full-product find-and-replace with QA cost.

---

## Q8 — Approval workflows → **One small generic engine**

**Decision.** Build a single approvals mechanism in M1: an `approvals` table plus
a per-type policy (what triggers approval, threshold, approver role, escalation,
timeout). Reused by form verification, stock adjustments, count variance,
purchase orders, time-off and job reopening.

Roughly two weeks, versus five separate half-implementations that diverge.

**Hard boundary:** no visual workflow designer, no branching graphs, no parallel
approval chains in v1. That is a product in itself and it is how this kind of
engine consumes a quarter.

---

## Q9 — PowerSync commercially → **RESOLVED: adopt directly, spike cancelled (ADR-015)**

At a solo part-time pace, a two-week spike that might end in hand-writing a sync
engine is a risk the project cannot absorb. PowerSync is adopted without
evaluating the alternative. The two conditions below still apply as design
constraints.

**Original reasoning.** Yes. Even at a four-figure monthly fee, 6–10 engineering weeks at
regional rates is USD 25–50k of build plus permanent maintenance and an entire
class of bugs that surface only in the field.

Two conditions on adoption, verified during the M0 spike:
1. The sync layer sits behind our own interface, so a swap to a custom engine is
   a contained project rather than a rewrite.
2. A self-hosted or exportable path exists, so a change in vendor terms is not an
   existential event.

The spike must exercise tenant-scoped sync rules, since incorrect rules here
would be a cross-tenant leak in the highest-risk component.

---

## Q10 — Device policy → **BYOD, designed for, no MDM dependency**

**Decision.** Support personal Android devices as the primary case, because that
is what field technicians in the region actually carry. Company-issued devices
are supported but never required.

Consequences, all already in `08-mobile-and-offline.md`: encrypted local SQLite,
biometric or PIN unlock, a deliberately narrow local data window, and remote wipe
that clears **app data only** — never the device. No MDM enrolment requirement,
because a technician will not enrol their personal phone and requiring it kills
adoption.

---

## Q11 — Location tracking during pilot → **Off, entirely**

**Decision.** No location features enabled for any pilot tenant. Location is
captured only at status transitions and form submission, as designed, and that
capture is disclosed in the app.

The pilot is where adoption habits and engineer trust are formed. An engineer who
believes the app is a surveillance tool will work around it, and the honest
feedback the pilot exists to produce disappears. Revisit after GA with explicit
tenant opt-in and a staff-notification obligation.

---

## Q12 — Payment gateway → **DEFERRED: not needed until there is someone to bill**

Billing is out of v1 entirely (ADR-015); early customers, if any, are invoiced
manually. Start the gateway application about a month before the first paying
customer — it needs a trade licence and takes 3–6 weeks. The choice below stands
for when that day comes.

### For when billing ships

**Decision.** Tap covers UAE cards, Saudi **mada** and Kuwaiti KNET through one
integration, which matches the Q3 sequence exactly. Stripe is added later for
international customers. Both sit behind the `PaymentProvider` interface.

**Manual bank-transfer invoicing ships from day one** and will carry more revenue
than cards in the first year — Gulf B2B buyers pay annually against a tax invoice
after a procurement cycle.

**Start the gateway application at kickoff.** It requires a trade licence and a
corporate bank account and takes 3–6 weeks. It is a classic launch blocker
discovered too late.

---

## Q13 — E-invoicing provider → **None. Removed from v1**

**Decision.** No e-invoicing integration in v1, on the Q3 reasoning: a UAE entity
has no current mandate, and Saudi B2B sales are reverse-charge so no ZATCA
obligation falls on our own invoices.

Revisit when any of these becomes true:
- the UAE mandate takes effect for our size of business;
- we register a Saudi entity;
- we ship tenant-to-customer invoicing in Phase 3, at which point tenants need
  compliant output and we integrate a certified provider rather than build one.

The `invoices.einvoice_*` fields stay in the data model so this is a feature, not
a migration.

**This must be confirmed with a UAE/KSA tax advisor before the first invoice is
issued.** It is the single highest-value item on this list to verify, because it
removes an entire workstream from the launch.

---

## Q14 — Pricing → **Published hypothesis, validated with design partners in M1–M2**

Per-seat, split by field and office, priced in AED and SAR:

| | **Starter** | **Professional** | **Enterprise** |
|---|---|---|---|
| Monthly | AED 399 | AED 1,299 | from AED 3,500 |
| Included | 5 field + 2 office | 15 field + 5 office | Custom |
| Extra field seat | AED 55 | AED 45 | Negotiated |
| Extra office seat | AED 139 | AED 119 | Negotiated |
| Annual | 2 months free | 2 months free | Negotiated |

Sanity check — a 40-engineer contractor on Professional pays roughly
AED 2,400/month, about **USD 16 per engineer per month**. Well under
ServiceTitan-class pricing, comfortably above cost to serve.

Saudi pricing is near-identical in SAR. Egypt is priced independently in EGP at
roughly 25–30% of Gulf levels and reviewed quarterly for FX, never converted.

Validate against real willingness to pay during the design-partner conversations
in M1–M2, not at M6.

---

## Q15 — Legal entity → **NOT NEEDED YET**

A registered company — trade licence plus business bank account — is required to
legally take money, sign customer contracts, obtain a payment gateway, and issue
tax invoices your customers can claim. It is **not** required to write code, run
a demo, or talk to prospects.

**So: not now.** Roughly a month before the first paying customer. It carries an
annual cost and accounting obligations from the day it exists, so registering
early buys nothing and costs real money.

### For when that day comes

**Decision.** IFZA, Meydan or DMCC. Indicatively AED 12,500–25,000 per year for
licence and visa allocation, set up remotely in days.

What it buys: a corporate bank account, payment gateway eligibility, a credible
contracting entity for Gulf enterprise buyers, 0% corporate tax below AED 375k
profit and 9% above, and no immediate e-invoicing burden.

Also required before the first customer: Terms of Service, Privacy Policy, DPA,
and a Master Services Agreement template. Budget USD 3–6k with a regional
technology lawyer.

**Confirm with a UAE corporate advisor.** Free-zone choice affects visa quota,
bank acceptance and whether you can invoice UAE mainland customers without a
local partner.

---

## Q16 — Name and trademark → **Check before any brand spend**

"Sanad" (سند — support) is an excellent, meaningful regional name. It is also
widely used: several established regional companies operate under it across
aviation, finance and insurance.

**Action, worth two hours now:** search the UAE Ministry of Economy and Saudi
Authority for Intellectual Property registries in classes 9 and 42, and secure a
distinctive wordmark — a compound such as **Sanad Field** materially improves
registrability. Acquire the domain before announcing anything.

Discovering a conflict after launch means rebranding a product customers have
already installed on their phones.

---

## Q17 — Licensing → **Proprietary**

Confirmed. The defensibility here is domain depth, regional compliance and
customer data gravity — none of which open-core protects, and all of which it
complicates.

---

## Q18 — On-premise edition → **Keep the path, do not sell it before GA + 6 months**

The Docker build stays green in CI from day one (ADR-003), so the option exists.
But single-tenant deployments consume support capacity a small team does not
have.

When it is sold, floor it at **USD 30–50k per year**. A customer unwilling to pay
that does not genuinely require data residency — they are expressing a
preference, and the correct answer is the shared cloud.

---

## Q19 — Template library ownership → **Paid domain expert, not a developer**

**Decision.** Contract a senior HVAC or FM maintenance supervisor to author and
review the ten starter templates, for roughly USD 1–2k. Ideally a supervisor at a
design partner, who will do it for the relationship.

These templates are a sales asset that shortens every trial. A developer writing
an HVAC preventive-maintenance checklist from a web search produces something a
technician will spot as wrong in ten seconds.

---

## Q20 — Support model → **WhatsApp first, Sunday–Thursday, Arabic and English**

**Decision.**
- Hours: Sun–Thu, 08:00–18:00 Gulf Standard Time. Note the working week — most
  support tooling defaults to Mon–Fri and will be wrong.
- Channels: **WhatsApp Business as the primary channel**, plus in-app chat.
  Email is a fallback; in this market, business users do not email support.
- Languages: Arabic and English, both staffed.
- On-call: rotation among the four engineers, SEV1 and SEV2 only.
- Every ticket carries tenant context automatically — plan, app version, recent
  errors, last sync per device.

---

## Remaining inputs

**None blocking.** Every question that gated the start of work is answered.
Phase A in `15-roadmap.md` can begin.

Three items become relevant later, none of them now:

1. **Country of residence and registration** (Q15) — about a month before the
   first paying customer, not before.
2. **Tax advisor confirmation** of the Q13 reverse-charge reading — before the
   first invoice is issued.
3. **Trademark search** (Q16) — before any public launch or brand spend. "Sanad"
   is used by several established regional companies, so this is worth two hours
   before the name appears anywhere public.
