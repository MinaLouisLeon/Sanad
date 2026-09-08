# 04 — Dynamic Forms

The highest-value and highest-risk module. A company admin designs forms with no
developer involvement; engineers complete them offline; the output is queryable
data plus a branded PDF the customer accepts as evidence of work done.

## 1. The three rules

**1. Published versions are immutable.** Editing a published form produces a new
version. Every submission stores `form_version_id`. A 2026 submission must render
in 2031 exactly as it was captured, with the labels and options that existed then.
Mutating a live schema silently corrupts historical records and is unrecoverable
once customers have data.

**2. The schema is declarative and renderer-agnostic.** No HTML, no CSS, no
platform-specific hints. One schema drives three renderers: web, React Native,
and PDF. If a renderer needs something the schema cannot express, the schema
gains a semantic property — never a styling escape hatch.

**3. Submissions are stored twice.** `data jsonb` is the source of truth.
`form_submission_values` is a flattened projection for reporting. Building only
the first makes cross-submission analytics impossible at scale; building only the
second loses fidelity.

## 2. Schema structure

```
FormSchema
  key, version, title_i18n, description_i18n
  settings:
    allow_draft, require_signature, require_geo, allow_offline,
    max_photos_per_field, editable_after_submit_minutes
  sections[]:
    key, title_i18n, description_i18n, repeatable, min_repeat, max_repeat,
    visible_when: <Condition>
    fields[]:
      key                  stable identifier, never reused or renamed
      type                 see §3
      label_i18n, help_i18n, placeholder_i18n
      required             bool | <Condition>
      visible_when         <Condition>
      readonly_when        <Condition>
      default_value        literal | { source: "asset.attributes.capacity" }
      validation           { min, max, minLength, maxLength, pattern,
                             decimals, custom }
      config               type-specific (options, units, accept, precision…)
      scoring              { weight, answer_scores: { option_key: points },
                             critical: bool }
```

`Condition` is a small serializable expression tree evaluated identically on web,
mobile and server:

```
{ all: [ { field: "equipment_running", op: "eq",  value: false },
         { any: [ { field: "fault_code", op: "isNotEmpty" },
                  { field: "severity",   op: "in", value: ["high","critical"] } ] } ] }
```

Operators: `eq ne gt gte lt lte in notIn contains isEmpty isNotEmpty between`.
The evaluator lives in `packages/forms-engine` and is shared by all three
renderers plus server-side validation. **Server-side re-validation is mandatory** —
a mobile client is not a trust boundary.

## 3. Field types

| Type | Config | Field notes |
|---|---|---|
| `text` / `textarea` | pattern, length | Arabic input, RTL-aware |
| `number` | min, max, decimals, unit | Big numeric keypad on mobile |
| `select` / `multiselect` | options, allow_other | Options carry stable keys, not labels |
| `boolean` | Yes/No/NA | NA matters — "not applicable" is not "no" |
| `date` / `time` / `datetime` | min, max | Gregorian + optional Hijri display |
| `photo` | max_count, annotation, require_geo | Compress client-side, EXIF stripped except geo, annotation overlay |
| `signature` | require_name, require_role | Vector + raster, with audit metadata (§7) |
| `geolocation` | accuracy_threshold | Captured automatically alongside submission |
| `barcode` | symbologies, link_to (item/asset) | Resolves to a real record, not just a string |
| `file` | accept, max_size | Documents, certificates |
| `rating` | scale, icon | Condition scoring |
| `checklist` | items, require_all | Compact multi-pass/fail |
| `calculated` | expression | Read-only, computed from sibling fields |
| `reference` | entity (asset/item/customer/user), filter | Real foreign key, enables analytics |
| `table` | columns | Fixed-column repeating grid |
| `section:repeatable` | min, max | e.g. one block per unit inspected |
| `instruction` | rich text, image | Guidance, captures no data |

Deliberately excluded from v1: nested repeatables more than one level deep,
cross-form lookups, and arbitrary scripting. Each is a large complexity
multiplier with narrow demand.

## 4. Versioning and lifecycle

```
draft ──publish──▶ published (immutable) ──▶ archived
  ▲                      │
  └───── new draft ◀─────┘  (edit creates version N+1 as a draft)
```

- Drafts are freely editable; published versions never change.
- `form_templates.current_version_id` points at the version used for new
  submissions.
- In-progress draft submissions keep their original version. An engineer
  half-way through an inspection when an admin publishes v3 finishes on v2.
- Archiving stops new submissions; existing ones remain readable and renderable.
- Publishing produces a **diff summary** ("2 fields added, 1 option removed,
  scoring changed") so admins understand the reporting impact before confirming.
- **Field keys are permanent.** The builder blocks renaming a key on a published
  form — it may only change the label. Reusing a retired key is rejected. This is
  what keeps five years of analytics coherent.

## 5. Builder UX (admin, web)

- Drag-and-drop canvas, palette of field types, live preview toggling between
  phone and PDF.
- Duplicate a section, duplicate a form, import from the platform template
  library.
- Conditional-logic editor as plain sentences: *"Show **Fault Code** when
  **Equipment Running** is **No**"* — not a JSON editor.
- Validation before publish: unreachable fields, circular conditions, scoring
  that cannot reach its maximum, required fields hidden by their own condition.
- Test mode: fill the form as an engineer would, then discard.
- Bilingual authoring: every label has Arabic and English side by side; publish
  warns about missing translations rather than blocking.

## 6. Scoring and pass/fail

For inspection and audit use cases:

- Per-answer points and per-field weights.
- Score is `earned / applicable`, where NA answers are excluded from the
  denominator — otherwise NA silently penalizes.
- Pass threshold per form; sections can carry their own thresholds.
- **Critical questions**: failing one forces overall failure regardless of score.
- Failing an item can auto-generate a corrective-action job linked to the asset —
  this is the loop that turns inspections into revenue.

## 7. Submissions

Lifecycle: `draft → submitted → verified | rejected` (rejected returns to the
engineer with a reason).

Every submission records: form version, submitter, device id, start and submit
timestamps, submission GPS with accuracy, linked job/task/asset/site, and a
`client_uuid` for offline idempotency (replaying the same submission never
creates duplicates).

**Signature integrity** matters because these documents get disputed. Each
signature stores: the vector strokes, a rendered PNG, signer name and role,
timestamp, GPS, device id, and a SHA-256 hash over the rendered form content at
signing time. If the underlying data is later amended, the hash mismatch is
visible on the PDF. This converts a picture of a squiggle into defensible
evidence.

**Editability:** editable by the submitter for a configurable window (default 30
minutes) or until a supervisor verifies. After that, corrections create an
amendment linked to the original — the original is never overwritten. Every edit
is audited with before/after.

## 8. Reporting projection

On submit (and on any amendment), a background job flattens `data` into
`form_submission_values`: one row per field per repeat index, with the value
written into the typed column matching its field type.

This enables the queries customers actually ask for:
- "All inspections where refrigerant pressure exceeded 40 bar, last 6 months"
- "Failure rate of question 7 by engineer, by site, by asset type"
- "Every asset whose last inspection scored below 70%"

The projection is derived and fully rebuildable. If it drifts, drop and rebuild
from `data`.

## 9. PDF export

- Rendered by the containerized Chromium service (see `01-architecture.md`),
  triggered on verification or on demand, cached as a file record.
- Tenant branding: logo, colors, header/footer, optional customer branding.
- Correct Arabic shaping and RTL layout — this requires a font with full Arabic
  support (IBM Plex Sans Arabic or Noto Naskh) embedded in the container, and
  must be verified with real Arabic content, not lorem ipsum.
- Includes photos with captions, signatures with their metadata, score summary,
  GPS map thumbnail, and a footer stating form name, version and submission id.
- Optional QR code linking to an online verification page.
- Bulk export: filtered set → ZIP, generated asynchronously and delivered by
  notification.

## 10. Platform template library

Sanad ships starter templates that tenants clone into their own editable copies:
HVAC preventive maintenance, generator service, elevator monthly inspection,
electrical safety check, site handover, incident report, toolbox talk, vehicle
pre-trip check.

Cheap to produce, dramatically shortens trial time-to-value, and demonstrates
what the builder can do better than any documentation. Cloned templates are
independent — updating the platform template never mutates a tenant's copy.

## 11. Performance boundaries

Documented so they are designed for rather than discovered:
- Max 300 fields per form version (builder warns at 150).
- Max 50 repeat instances per repeatable section.
- Max 30 photos per submission (configurable per tenant).
- Submissions with more than 100 fields render progressively on mobile,
  section by section.
