# v0.5 · Phase 1 — Decide

## Inputs

- `docs/04-module-forms.md` §5, §7, §9
- v0.3's engine and versioning rules, unchanged by this version

## Tasks

### Builder
- [ ] Decide the canvas interaction: drag from a palette, reorder, nest into sections
- [ ] Decide how a field's properties are edited — a side panel, not a modal
- [ ] Decide how bilingual labels are authored: English and Arabic side by side
- [ ] Decide the missing-translation policy on publish — warn, do not block
- [ ] Decide how the conditional-logic editor reads as a sentence
      ("Show **Fault Code** when **Equipment Running** is **No**")
- [ ] Decide what pre-publish validation catches: unreachable fields, circular
      conditions, duplicate keys, required fields hidden by their own condition
- [ ] Decide how field keys are generated and how renaming is prevented in the UI
- [ ] Decide the preview modes: phone and PDF

### PDF
- [ ] Decide where the renderer runs — a container outside Vercel, per
      `docs/01-architecture.md`. Serverless execution limits make headless
      Chromium fragile
- [ ] Choose the Arabic font and confirm it is embedded in the container image
- [ ] Decide the PDF layout: header with branding, metadata block, sections,
      photos with captions, signatures with their metadata, footer with form name,
      version and submission id
- [ ] Decide when PDFs are generated — on verification, on demand, or both
- [ ] Decide caching: generated once, stored as a file record, regenerated only
      if the submission is amended
- [ ] Decide the signature integrity record: what is hashed and how a later
      amendment becomes visible

### Verification
- [ ] Decide who may verify, and whether verification is required per form or per
      job type
- [ ] Decide what rejection does — returns to the submitter with a reason
- [ ] Decide the edit window after submission and how amendments are recorded

## Exit criteria

- [ ] Builder interaction model decided and sketched
- [ ] Validation rule set written down
- [ ] PDF layout and rendering approach decided
- [ ] Arabic font chosen and confirmed available to the renderer
