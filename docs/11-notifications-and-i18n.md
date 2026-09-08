# 11 — Notifications & Localization

## 1. Notification architecture

Events are emitted by domain modules and consumed by the notification service.
Domain code never calls an email or SMS provider directly — it publishes
`job.assigned` and moves on.

```
Domain event → Rules (who cares?) → Preferences (which channels?)
             → Quiet hours → Template (locale) → Provider adapter → Delivery record
```

Every send produces a `notification_deliveries` row with provider message id and
status, updated by provider webhooks. Without this, "the customer says they never
got it" is unanswerable.

## 2. Channels

| Channel | Use | Notes |
|---|---|---|
| **In-app** | Everything | Always written; the durable record |
| **Push** | Mobile-urgent: assignment, schedule change, emergency | Minimal content on lock screens |
| **Email** | Digests, reports, PDFs, invitations, invoices | Attachments |
| **WhatsApp** | Customer-facing and engineer-urgent | **The dominant business channel in the region** |
| **SMS** | Fallback when WhatsApp fails; OTP | Costly; reserve for high value |

### WhatsApp specifics

Not optional in this market — a customer appointment reminder sent by email in
Egypt or Saudi Arabia is a message nobody reads.

- Meta WhatsApp Business Platform through a BSP (Unifonic regionally, or Twilio).
- **Business-initiated messages require pre-approved templates**, submitted and
  approved by Meta before use. This has a lead time of days, so templates must be
  registered during development, not at launch.
- Templates are per-language; Arabic and English variants of each.
- A 24-hour customer service window allows free-form replies after an inbound
  message.
- Phase 3: inbound handling so a customer can reply "confirm" to an appointment
  and update the job automatically.
- Costs are per-conversation — meter it and expose it in plan allowances.

## 3. Preferences and quiet hours

- Per user, per event category, per channel.
- **Quiet hours** default to outside working hours, with an override for
  emergency-priority events only. Waking an engineer at 2 a.m. for a routine
  schedule change is how push permission gets revoked.
- Digest options for non-urgent categories (daily summary rather than a stream).
- Tenant admins set organizational defaults; users adjust within them.
- Every external message carries an unsubscribe or preference link where the
  channel requires it.

## 4. Event catalogue (v1)

**Field ops:** job assigned, reassigned, rescheduled, cancelled, emergency
raised, SLA at 80%, SLA breached, job completed, job failed visit, verification
requested, work rejected.
**Forms:** submission rejected, critical failure detected, form published.
**Inventory:** stock below reorder point, van stock negative, batch expiring,
count variance needs approval, PO received.
**Meetings:** invitation, reminder, updated, cancelled, minutes published, action
item assigned, action item overdue.
**Customer-facing:** appointment confirmed, engineer en route (with ETA), work
completed with PDF, feedback request.
**Account:** invitation, password reset, new device login, payment failed,
invoice issued, trial ending.

"Engineer en route with ETA" deserves highlighting — it is the single most
appreciated customer-facing notification in field service and costs almost
nothing to build once status transitions exist.

## 5. Localization

### Locales — split by audience (ADR-011)

The two audiences do not share a language, and treating them as one was an early
mistake in this plan.

| Surface | Primary | Also | Later |
|---|---|---|---|
| Web app — office, dispatch, admin, reports | **Arabic** | English | — |
| Customer-facing PDFs and notifications | **Arabic** | English | — |
| **Mobile app — field technicians** | **English (simple)** | Arabic | **Urdu, Hindi** (Phase 3); Malayalam, Tagalog, Bengali (Phase 4) |

In the Gulf launch market the field technician workforce is predominantly South
Asian expatriate and largely does not read Arabic, while the office audience —
owner, operations manager, dispatcher — and the customer receiving a service
report are Arabic-speaking. Arabic remains primary for field staff in Egypt,
Jordan and the Levant.

Consequences:
- **The mobile app is written in deliberately simple English**: short labels, no
  idiom, no jargon, icon-supported. It is read by a second-language speaker in
  bad light under time pressure. This is enforced in code review, not left to
  taste.
- Urdu is RTL, so the RTL system serves it directly; Hindi is LTR.
- Form templates support **per-language label authoring** as a headline feature:
  a tenant asks the question in English for the technician answering it, and
  prints it in Arabic on the customer's report.
- French (`fr`) and Turkish (`tr`) move behind Urdu and Hindi.

Locale is **per user**, defaulting to the tenant's. Notifications render in the
**recipient's** locale — an Urdu-speaking technician and an Arabic-speaking
dispatcher work the same job in different languages.

### RTL

- CSS **logical properties** everywhere (`margin-inline-start`, not
  `margin-left`). This is a code-review rule, enforced by lint where possible.
- `dir` set from the locale at the document root; Tailwind `rtl:` variants only
  for genuine exceptions.
- Icons with direction (arrows, chevrons, progress) mirror; icons with meaning
  (logos, clocks, media controls) do not.
- **The dispatch board timeline runs right-to-left in Arabic.** This is the
  hardest RTL surface in the product and must be designed and tested from the
  start, not patched later.
- Charts, sliders and drag interactions all need explicit RTL handling.
- Mixed-direction content (an Arabic sentence containing an English model number)
  needs Unicode bidi isolation to avoid mangled rendering.

### Typography

- Arabic: IBM Plex Sans Arabic or Noto Sans Arabic — chosen for a wide weight
  range and a Latin companion that pairs cleanly.
- Arabic needs roughly 15% more line height than Latin at the same size; a shared
  type scale that ignores this makes Arabic text feel cramped.
- The PDF renderer must embed a full Arabic font, and PDF output must be reviewed
  with real Arabic content — Arabic shaping in headless Chromium is a known
  source of broken output.

### Numbers, dates and calendars

- Numerals: **Western digits (0–9) by default**, with Arabic-Indic (٠–٩) as a
  tenant option. Gulf business contexts overwhelmingly use Western digits;
  defaulting to Arabic-Indic looks authentic and annoys users.
- **Hijri calendar** displayed alongside Gregorian where relevant (dates,
  reports, PDF headers), tenant-configurable. Gregorian remains the storage and
  scheduling basis.
- Storage is always UTC; display always in the tenant or branch timezone;
  the timezone is always visible on scheduled times to avoid dispatch mistakes.

### Regional calendar rules — operationally critical

These are not cosmetic. They directly drive scheduling, SLA clocks, recurrence
generation and utilization reporting.

| Rule | Detail |
|---|---|
| **Weekend** | Friday–Saturday in Saudi Arabia, Egypt and most of the Gulf. Configurable per tenant and per branch — a defaulted Sat–Sun weekend generates a week of wrongly scheduled jobs on day one |
| **Ramadan hours** | Reduced statutory working hours during Ramadan; the working-hours calendar supports date-ranged overrides |
| **Islamic holidays** | Eid al-Fitr and Eid al-Adha move on the Gregorian calendar and are often confirmed only days ahead. Holiday calendars must be editable at short notice, and recurrence regeneration must handle a late change gracefully |
| **National holidays** | Per-country calendars, seeded and maintained by the platform, cloned and editable per tenant |
| **Prayer times** | Optional per-branch break windows. A small touch that regional users notice immediately and no international competitor offers |

## 6. Content and template management

- Templates versioned, per channel, per locale.
- Variables resolved from a documented event payload with strict escaping.
- Tenants may override customer-facing templates within their branding; system
  templates (security, billing) are not overridable.
- A preview-and-test-send facility for admins before any template goes live.
- Rendering failures fall back to the system default and raise an alert rather
  than dropping the message silently.
