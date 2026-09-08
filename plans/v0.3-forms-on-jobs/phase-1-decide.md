# v0.3 · Phase 1 — Decide

The most consequential design phase in the project. Decisions here are visible in
customer data for as long as the product exists.

## Inputs

- `docs/04-module-forms.md` — read it in full before starting
- ADR-007 (immutable versions)

## Tasks

### Schema shape
- [ ] Fix the `FormSchema` TypeScript types: sections, fields, settings
- [ ] Fix the `Condition` expression tree and its operator set
- [ ] Decide the field `config` shape per field type
- [ ] Decide the validation rule vocabulary
- [ ] Decide how bilingual labels are stored — `label_i18n` per field
- [ ] Decide `default_value`, including values sourced from the linked asset

### Versioning — the rules that cannot be retrofitted
- [ ] Confirm: publishing creates a new version; published versions are never mutated
- [ ] Decide what happens to a draft submission when a new version publishes
      (recommendation: it finishes on the version it started)
- [ ] Decide the field key rules: permanent, never renamed, never reused. Decide
      how the system **enforces** this rather than documenting it
- [ ] Decide how a submission references its version, and how old versions render

### Storage
- [ ] Confirm the dual storage decision: `data` JSONB plus the
      `form_submission_values` projection
- [ ] Decide when the projection is written — synchronously on submit, or by a job
- [ ] Decide the typed column layout in the projection and how repeat index is held
- [ ] Decide how the projection is rebuilt from scratch if it drifts

### Evaluation
- [ ] Decide that the conditional-logic evaluator is written **once** in
      `forms-engine` and shared by renderer and server. Divergence here produces
      submissions rejected in the field
- [ ] Decide the server-side re-validation policy: everything the client checks,
      the server checks again

### Integration
- [ ] Decide how job types declare required forms
- [ ] Decide the completion gate's exact rule
- [ ] Decide whether a submission can exist without a job (recommendation: yes —
      standalone inspections are a real case, and the column is nullable)

### Starter templates
- [ ] Choose the two or three seeded templates for this version
- [ ] Write them as JSON against the finalised schema

## Exit criteria

- [ ] Schema types written and reviewed
- [ ] Versioning rules written down unambiguously, with enforcement decided
- [ ] Projection design settled
- [ ] Seeded templates written as JSON
- [ ] An ADR recorded for anything that diverges from `docs/04-module-forms.md`
