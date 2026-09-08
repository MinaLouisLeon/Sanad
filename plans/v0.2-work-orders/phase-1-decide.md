# v0.2 · Phase 1 — Decide

## Inputs

- v0.1 released
- `docs/05-module-field-ops.md`, `docs/03-data-model.md` §4–5

## Tasks

### Domain decisions
- [ ] Confirm the job status set for v0.2 and which transitions are legal from each
- [ ] Decide which transitions require a reason (`on_hold`, `cancelled`,
      `failed_visit` at minimum)
- [ ] Decide the job numbering scheme — per-tenant sequence, format, and whether
      it is ever reused
- [ ] Decide what `completed` requires now, and confirm the gate tightens in v0.3
      once forms exist
- [ ] Decide how sites nest — building, floor, room — and how deep the UI shows
- [ ] Decide the asset tag format and what the QR code encodes
- [ ] Decide whether a job may exist without a site or asset (recommendation: yes,
      for ad-hoc work; the field is nullable)

### Data
- [ ] Confirm the DDL written in v0.1 covers everything needed; migrate only what
      it missed
- [ ] Decide the indexes for the job list's real query shapes
- [ ] Decide the search approach for customers, sites, assets and jobs — Postgres
      full-text with an Arabic configuration

### Interface
- [ ] Decide the job list layout, its default columns and its default filters
- [ ] Decide the job detail layout, and which fields are editable in which status
- [ ] Decide how status transitions are presented — a primary action, not a dropdown
- [ ] Decide the schedule view for this version: a grouped list by day, no dragging

## Exit criteria

- [ ] Status machine written out as an explicit transition table
- [ ] Job numbering decided
- [ ] Screen layouts sketched
- [ ] Any new decisions recorded as ADRs
