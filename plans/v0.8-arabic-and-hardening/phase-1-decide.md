# v0.8 · Phase 1 — Decide

This phase is an audit rather than a design exercise. Produce a list of defects
and decide which are fixed now.

## Tasks

### Arabic audit
- [ ] Walk every web screen in Arabic on a desktop, and log every defect
- [ ] Walk every mobile screen in Arabic on a real phone, and log every defect
- [ ] Check terminology against `docs/17-terminology-ar.md` and log every
      inconsistency
- [ ] Check every notification template in Arabic
- [ ] Check the PDF in Arabic, including a document with mixed Arabic and Latin
- [ ] Confirm numerals, dates and currency follow the tenant's configuration

### Performance audit
- [ ] Seed a production-shaped dataset: a company with years of jobs,
      submissions, movements and photos
- [ ] Measure the slowest routes; record actual p95 figures
- [ ] Review query plans on every list screen; find sequential scans
- [ ] Measure mobile sync time for a day's work on a throttled connection
- [ ] Measure cold start on a low-end Android device

### Security audit
- [ ] Re-read every route for server-side permission and scope checks
- [ ] Confirm every table still has RLS and isolation coverage — six versions of
      drift is where gaps appear
- [ ] Review file upload handling: type validation, size caps, URL scoping
- [ ] Review rate limiting coverage
- [ ] Run dependency and secret scanning
- [ ] Review what reaches logs and Sentry for leaked customer data

### Quality audit
- [ ] List screens missing loading, empty or error states
- [ ] List unhandled error paths
- [ ] Run an accessibility check on the web: contrast, keyboard, labels

## Exit criteria

- [ ] A single defect list, prioritised
- [ ] Agreement on what is fixed in v0.8 and what is deferred past v1.0
- [ ] Baseline performance figures recorded, to compare against after fixes
