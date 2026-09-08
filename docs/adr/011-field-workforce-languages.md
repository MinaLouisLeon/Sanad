# ADR-011 — Office is Arabic-first; the field app is English-first with South Asian languages

**Status:** Accepted · 2026-09-08 · **Corrects an assumption in ADR-002**

## Context
ADR-002 established Arabic as the primary language and design baseline. Applied
uniformly, that assumption is wrong for one of the two audiences.

In the Gulf — the launch market under ADR-009 — the **field technician workforce
is predominantly South Asian expatriate**: Indian, Pakistani, Bangladeshi,
Nepali and Filipino. HVAC technicians, elevator engineers and FM operatives in
the UAE and Saudi Arabia largely do not read Arabic. Their working language is
English, often as a second language, with Hindi, Urdu, Malayalam and Tagalog as
first languages.

Meanwhile the **office audience is Arabic-speaking**: the owner, the operations
manager, the dispatcher, and the customer receiving a service report.

Assumption 5 in `16-open-questions.md` — "Arabic is the primary interface
language for field staff" — was incorrect for the Gulf. It holds for Egypt, Jordan
and the Levant, and increasingly for Saudi nationals entering technical roles
under Saudization.

## Decision
Split the language priority by audience.

| Surface | Primary | Also | Later |
|---|---|---|---|
| **Web app** (office, dispatch, admin, reports) | **Arabic** | English | — |
| **Customer-facing PDFs and notifications** | **Arabic** | English | — |
| **Mobile app** (field technicians) | **English (simple)** | Arabic | **Urdu, Hindi** (Phase 3), Malayalam, Tagalog, Bengali (Phase 4) |
| **Form content** | Authored bilingually by the tenant | | |

The **user's** locale governs rendering, not the tenant's — an Urdu-speaking
technician and an Arabic-speaking dispatcher use the same job through the same
system in different languages. The architecture already supports this
(`11-notifications-and-i18n.md` §5); this ADR changes which catalogs are
prioritized and how the mobile app is written.

## Consequences
- **The mobile app is written in deliberately simple English**: short labels,
  no idiom, no jargon, icon-supported. It is being read by a second-language
  speaker in poor light under time pressure. This is a copywriting constraint,
  and it is enforced in review.
- RTL work remains fully required — it is driven by the web app, the PDFs and
  Arabic-speaking technicians in Egypt and the Levant.
- Form templates gain **per-language label authoring** as a first-class concern,
  not an afterthought: a tenant writes a question in Arabic for the report and in
  English for the technician who answers it. This was already in the schema and
  is now a headline feature.
- Urdu and Hindi catalogs are scheduled for Phase 3, ahead of French and Turkish,
  which move to Phase 4 or later.
- Urdu is RTL, so the RTL system serves it directly; Hindi is LTR.
- Design-partner ride-alongs must confirm the actual language mix per partner
  rather than assuming it (`18-design-partner-program.md` §7).
