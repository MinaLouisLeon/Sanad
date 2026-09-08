# v0.5 — Form Builder & PDF

**Goal:** a company admin builds their own forms with no developer, and the
completed form becomes a branded PDF the customer accepts as evidence.

v0.3 built the engine. This version puts a face on it and produces the artefact
customers actually pay for.

## Ships with

- Drag-and-drop form builder with live preview
- Remaining field types: multiselect, time, datetime, geolocation, rating,
  checklist, photo and signature configuration
- Conditional-logic editor written as plain sentences, not JSON
- Publish flow: pre-publish validation, diff summary, change note
- Submission verification and rejection by a supervisor
- PDF rendering service with tenant branding
- PDF output in English **and Arabic**, with correct shaping and RTL
- Reporting projection extended for cross-submission queries

## Does not ship with

Repeatable sections, calculated fields, reference fields, tables, barcode fields,
scoring and pass/fail, the platform template library, bulk export. All ADR-015
depth cuts, deferred past v1.0.

## Exit criteria

- [ ] An admin builds a working form from scratch without touching code
- [ ] The builder refuses to publish an invalid form and explains why
- [ ] Renaming a published field key is impossible through the UI
- [ ] The diff summary describes what changed before publishing
- [ ] A completed form renders as a PDF with the tenant's branding
- [ ] **Arabic PDF output is correct** — shaping, RTL layout, no boxes or
      reversed text — verified with real Arabic content
- [ ] A supervisor can verify or reject a submission with a reason

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
