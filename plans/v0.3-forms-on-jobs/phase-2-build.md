# v0.3 · Phase 2 — Build

## Tasks

### `packages/forms-engine`
- [ ] Schema types and Zod schemas for validating a schema itself
- [ ] Condition evaluator — pure, dependency-free, exhaustively unit tested
- [ ] Field validation given a schema and an answer set
- [ ] Visibility resolution across a whole form
- [ ] Helpers: list fields, resolve a field by key, diff two versions

### Database
- [ ] `form_templates`, `form_versions`, `form_submissions`,
      `form_submission_values`
- [ ] RLS policies and isolation coverage
- [ ] A database-level guard preventing a published version's schema from being
      updated. Application discipline is not sufficient for a rule this important
- [ ] GIN index on `form_submissions.data`
- [ ] Indexes on the projection for reporting filters

### Domain
- [ ] Template service: create, list, archive
- [ ] Version service: create draft, publish, fetch, diff
- [ ] Publish validation: unreachable fields, circular conditions, duplicate keys,
      renamed or reused keys
- [ ] Submission service: start draft, save progress, submit, fetch
- [ ] Server-side re-validation on submit using the same engine as the client
- [ ] Projection writer, plus a rebuild command
- [ ] Job type required-forms wiring
- [ ] Completion gate enforced in the job service

### Web
- [ ] Form renderer component driven purely by schema
- [ ] Field components for the v0.3 types
- [ ] Conditional visibility applied live as answers change
- [ ] Inline validation with messages in both languages
- [ ] Autosave of drafts
- [ ] Template list and version history — read-only, no builder yet
- [ ] Submission list and detail view, rendered against the submission's own version
- [ ] Forms surfaced on the job detail screen, with required ones marked

### Seeds
- [ ] Load the JSON starter templates as published versions
- [ ] Generate sample submissions across more than one version, to prove old
      versions still render

## Exit criteria

- [ ] A form renders from schema and submits
- [ ] Publishing a change creates v2 and leaves v1 byte-identical
- [ ] A v1 submission still renders against v1 after v2 exists
- [ ] Conditional logic behaves identically in the browser and on the server
- [ ] The completion gate blocks a job missing a required form
