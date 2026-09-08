# ADR-014 — English is the default language; English and Arabic are the only supported locales

**Status:** Accepted · 2026-09-08 · **Supersedes ADR-011** · Amends ADR-002

## Context
ADR-002 made Arabic the primary language and design baseline. ADR-011 then split
that by audience — Arabic-first web, English-first mobile — and scheduled Urdu,
Hindi, Malayalam, Tagalog, Bengali, French and Turkish across later phases.

The product owner has directed a simpler policy: **English is the default
language throughout, and the product supports English and Arabic only.**

## Decision

```
supported locales : en, ar
default locale    : en
```

- **English (`en`) is the default and the source catalog.** Message keys are
  authored in English; it is the fallback for any missing translation, on every
  surface — web, mobile, notifications, PDFs and the admin console.
- **Arabic (`ar`) is a fully supported, first-class locale**, not a partial
  translation. Parity is a release gate: no user-facing string ships without its
  Arabic counterpart.
- **No other locale ships.** Urdu, Hindi, Malayalam, Tagalog, Bengali, French and
  Turkish are removed from the roadmap entirely, not deferred.

**Locale resolution**, in precedence order:
1. The user's explicit choice.
2. The tenant's default locale, set at signup and changeable by an admin.
3. `en`.

A user's locale governs everything they see, and notifications render in the
**recipient's** locale — an Arabic-speaking dispatcher and an English-speaking
technician work the same job in their own languages.

## Rationale
Two locales instead of nine removes a large, permanent tax: every new string,
every screen review, every PDF template and every release gate is multiplied by
the locale count. English plus Arabic covers the office audience and the
technician audience across the launch markets, with English serving as the
working language of the Gulf's predominantly expatriate field workforce.

English as the base catalog also matches how the code is written: keys, source
strings and developer discussion are already in English, so the source of truth
and the default are the same artefact.

## Consequences

**Simplifications**
- One translation pair. Arabic parity is checkable mechanically in CI.
- The LTR layout is the primary path, which is the better-trodden one in the
  React, React Native and headless-Chromium ecosystems.
- No Devanagari or additional Indic font loading, and no third shaping engine in
  the PDF renderer.
- `packages/i18n` carries two catalogs; `defaultLocale = 'en'`,
  `locales = ['en', 'ar']`.

**Risks this introduces, and the controls for them**

RTL quality degrades when it stops being the design baseline. Arabic becomes the
locale nobody looks at until a customer complains. The following are therefore
**not optional**, and they are the price of this decision:

- CSS **logical properties only** (`margin-inline-start`, never `margin-left`),
  enforced by lint.
- Every screen is reviewed in Arabic RTL **before merge**, not before release.
- E2E journeys run in both locales (`14-testing-and-quality.md` §6); an
  RTL failure blocks the release exactly as an LTR one does.
- The dispatch board timeline, charts and drag interactions keep their explicit
  RTL handling — they remain the hardest RTL surfaces regardless of which locale
  is the default.
- Seed and demo data use **real Arabic content**, never Latin placeholder text.
- A CI check fails the build on any user-facing key present in `en` and missing
  in `ar`.

**Unchanged by this decision**
- The PDF renderer still embeds a full Arabic font and is still verified with
  real Arabic content — customer service reports are frequently Arabic.
- Form templates keep **bilingual label authoring**: a tenant may ask a question
  in English for the technician answering it and print it in Arabic on the
  customer's report.
- The Arabic terminology glossary (`17-terminology-ar.md`) is still required. Its
  role changes from source-of-truth to translation target, and it matters just as
  much — a wrong Arabic word for "dispatch" is wrong whichever language is the
  default.
- Regional calendar rules — Fri–Sat weekend, Hijri display, Ramadan hours, Eid
  holidays — are **locale-independent**. They follow the tenant's country, not
  its language, and are entirely unaffected (`11-notifications-and-i18n.md` §5).
- Mobile copy stays deliberately simple English: short labels, no idiom,
  icon-supported. It is read by a second-language speaker in bad light under time
  pressure, and that constraint came from the audience, not from the locale
  policy.
