# v0.3 — Forms on Jobs

**Goal:** a job carries a completed, versioned form.

The form builder UI arrives in v0.5. This version builds the engine underneath it
and proves it works, with templates defined as JSON by hand. That ordering is
deliberate: the versioning and validation rules are the part that cannot be
retrofitted, and they are worth getting right before any UI is layered on top.

## Ships with

- `packages/forms-engine`: schema types, validation, conditional-logic evaluator
- **Immutable published versions** from the first commit (ADR-007)
- Field types: text, textarea, number, select, boolean, date, instruction
- Conditional visibility
- Web form renderer
- Templates seeded as JSON, one per starter form
- Required forms attached to job types
- Submission capture, listing and viewing
- The completion gate: a job cannot complete until its required forms are submitted

## Does not ship with

Builder UI, photo, signature, geolocation, multiselect, repeatable sections,
scoring, PDF export. Photo and signature arrive with the mobile app in v0.4 since
that is where they are actually captured.

## Exit criteria

- [ ] A form template exists as a published, immutable version
- [ ] Editing a template creates a new version and leaves the old one untouched
- [ ] A submission renders identically against its own version after the template
      has moved on
- [ ] Conditional visibility behaves the same in the renderer and on the server
- [ ] Server-side validation rejects what the client would have rejected
- [ ] A job with a required form cannot be completed without it
- [ ] `form_submission_values` is populated and queryable across submissions

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
