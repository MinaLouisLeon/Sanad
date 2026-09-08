# ADR-016 — Versioned delivery: vertical slices, each with four phases

**Status:** Accepted · 2026-09-08 · Implements the sequencing left open by ADR-015

## Context

ADR-015 established what to build and at what depth, but not how the work is
organised. Two structures were available:

**One version, many phases.** Simpler to write down. Its defining property is
that nothing is releasable until the final phase, so "production" becomes a cliff
at the end of a long solo project.

**Multiple versions, each with phases.** Each version is a deployable increment.

## Decision

**Multiple versions, each with its own four phases.** Nine versions from an empty
repository to a system a real company uses, documented in
[`/plans`](../../plans/README.md).

Each version is a **vertical slice that works end to end** and is deployed to
production before the next begins. Each runs the same phases:

```
Phase 1  DECIDE    Schema, contracts and decisions — written down first
Phase 2  BUILD     Database → domain → API → UI
Phase 3  VERIFY    Tests, tenant isolation, manual QA in English and Arabic
Phase 4  RELEASE   Migrate, deploy, tag, release note, update docs
```

**No dates, durations or estimates appear anywhere in the plan.** A version is
done when its exit criteria are met.

## Rationale

- **Production stops being a destination.** It is entered at v0.1, so migrations,
  backups, RLS and deploys are exercised repeatedly instead of being invented
  once, at the end, under pressure. That repetition is what actually makes a
  system production-ready.
- **Integration is continuous.** Phases within a single version tend to be
  horizontal layers that only meet at the end. Versions are vertical slices that
  must work before the next begins. For four pillars built by one person, "it all
  integrates for the first time at the end" was the most dangerous available
  shape.
- **Every version is a non-failing stopping point.** Working in short sessions
  with long gaps, stopping mid-layer leaves an unusable system; stopping after a
  version leaves something real.
- **Versions are artefacts, not prose.** A version is a git tag, a migration set,
  a mobile build and a release note. Phases are headings in a document. Where the
  dominant risk is context loss between sessions, a plan that matches the actual
  artefacts drifts less.
- **Phase 1 protects against the real failure mode.** Returning to a written
  decision after three weeks away is cheap. Returning to half-written code is not.

## Consequences

- Release overhead is paid nine times rather than once. Mitigated by keeping the
  ritual small: migrate, deploy, smoke test, tag, write a short release note.
- Some work is touched more than once — the mobile app gains screens in v0.4,
  v0.6 and v0.7 — which is accepted as the cost of shipping end to end each time.
- Phase 4 is never skipped, even when nothing user-visible changed, because the
  ritual is what keeps production deployable.
- The isolation suite is extended in **every** version's Verify phase, never
  assumed to still be complete.
- Each version folder carries a `NOTES.md` written at the end of every session,
  and a `RELEASE.md` on shipping.
