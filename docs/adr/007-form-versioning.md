# ADR-007 — Published form versions are immutable

**Status:** Accepted · 2026-09-08

## Context
Company admins edit forms continuously. Submissions must remain accurate and
renderable for years, often as contractual or regulatory evidence.

## Decision
Publishing creates a new immutable `form_version`. Editing a published form
produces version N+1 as a draft. Every submission stores `form_version_id` and is
always rendered against that version. Field keys are permanent — they may not be
renamed or reused once published.

## Consequences
- A submission from 2026 renders identically in 2031, with the labels and options
  that existed at capture time.
- Reporting must be version-aware: comparing a field across versions requires
  the stable field key, which is why keys are immutable.
- Draft submissions in progress complete on the version they started with.
- Storage grows with version count; acceptable, as schemas are small.
- The builder must actively prevent key renaming and reuse, and must show a diff
  summary before publish so admins understand the reporting impact.
