# 16 — Open Questions

Decisions still needed. **Blocking** items must be answered before the
corresponding milestone starts.

## Blocking before M0

**Q1 — Team and budget.** How many engineers, and are they available full-time?
The roadmap assumes four. This is the single largest determinant of the timeline
and no other estimate is meaningful without it.

**Q2 — Design partners.** Do we have two or three real companies willing to
pilot? Building an FSM product without a design partner produces a plausible
product that nobody's operations team can actually use. The dispatch board and
the mobile job flow in particular need real users watching real dispatchers work.

**Q3 — Primary country for launch.** Saudi Arabia, UAE and Egypt have materially
different requirements: payment gateways, e-invoicing regimes, VAT rates, data
residency expectations and price points. Building for all three at once dilutes
all three. Which market do the first ten customers come from?

**Q4 — Which vertical do we optimize for first?** HVAC/MEP, facilities
management, elevators, medical equipment and solar O&M share 80% of the model but
differ in terminology, default forms and asset structure. Choosing one sharpens
onboarding, the template library and the sales pitch. The other verticals still
work — they simply are not the default.

## Blocking before M1

**Q5 — Is a customer-facing portal really Phase 3?** It is the strongest
differentiator in the plan and it is currently deferred. If design partners say
their clients demand visibility, moving a read-only portal into v1 is roughly
three weeks and may be worth the schedule cost.

**Q6 — Multi-currency per tenant?** Currently one currency per tenant. A
contractor operating in both Saudi Arabia and the UAE would need per-branch
currency. Cheap to model now, expensive to retrofit into costing and reporting.

**Q7 — Arabic terminology glossary.** Who owns it, and when? Needed before the
first user-facing screen. The words for job, work order, asset, dispatch,
inventory, van stock and submission must be fixed and consistent.

**Q8 — Approval workflows: generic engine or hard-coded?** Form verification,
stock adjustments, purchase orders, time-off and variance approvals all follow
the same pattern. One configurable engine costs perhaps two extra weeks now and
saves that repeatedly later. Recommendation: build the generic engine.

## Blocking before M3 (mobile)

**Q9 — PowerSync or a custom sync layer?** Resolved by the M0 spike (ADR-005),
but the commercial question needs an answer in advance: is a per-connection
vendor cost acceptable, given roughly 6–10 engineering weeks saved and a large
class of bugs avoided?

**Q10 — Device policy.** Company-issued devices or BYOD? BYOD changes the
security model materially — remote wipe, screenshot policy, and what is
acceptable to store locally.

**Q11 — Is location tracking enabled for pilot tenants?** Sensitive, per
`09-security-and-compliance.md` §7. It should be off by default; confirm the
pilot tenants agree and that their staff are informed.

## Blocking before M6 (billing)

**Q12 — Payment gateway selection.** Driven by Q3. Onboarding takes weeks and
requires commercial registration documents — start the application at project
kickoff, not at M6.

**Q13 — E-invoicing provider.** Which certified provider for ZATCA and ETA?
Building in-house is not recommended (`10-billing-and-plans.md` §6).

**Q14 — Pricing.** The tiers in the billing document are indicative. Real numbers
need competitor research and design-partner willingness-to-pay conversations.
This should happen during M1–M2, not at M6.

**Q15 — Legal entity and contracts.** Where is the company registered? Terms of
service, DPA and privacy policy need a lawyer familiar with the target
jurisdiction, and VAT registration is required before invoicing locally.

## Non-blocking, worth deciding early

**Q16 — Product name and brand.** "Sanad" (سند — support) is a strong regional
name. Trademark availability and domain acquisition should be checked before
brand work.

**Q17 — Open-core or fully proprietary?** Affects licensing and repository
structure. Assumed proprietary.

**Q18 — Do we sell an on-premise edition?** Assumed yes as an enterprise tier
(`01-architecture.md` §8). It shapes packaging discipline throughout, so decide
before habits form.

**Q19 — Ownership of the platform template library.** Someone with domain
expertise must author the starter forms. They are a sales asset, not a
developer task.

**Q20 — Support model at launch.** Hours, languages, channels, and who is on
call. Arabic-language support is expected in this market.

---

## Assumptions being made in the absence of answers

Documented so they can be challenged rather than silently inherited:

1. Launch market is Saudi Arabia first, with the UAE close behind; Egypt is a
   second wave with independent pricing.
2. The first vertical is HVAC/MEP and facilities-management contracting.
3. Tenants are 10–500 staff; nobody in the first year exceeds 500 users.
4. Engineers use company-issued or personal **Android** devices, weighted towards
   low-end hardware.
5. Arabic is the primary interface language for field staff; office staff are
   comfortable in either Arabic or English.
6. Customers will pay annually by bank transfer more often than monthly by card.
7. No customer in year one requires in-country data residency; the enterprise
   deployment path exists but is not exercised.
8. Internet connectivity at customer sites is unreliable and frequently absent.
