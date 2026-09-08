# v0.8 · Phase 2 — Build

Work the defect list. Nothing else.

## Tasks

### Arabic and RTL
- [ ] Fix every logged RTL layout defect
- [ ] Replace any remaining physical CSS properties with logical ones
- [ ] Apply the agreed terminology everywhere: UI, notifications, PDFs, emails
- [ ] Fix icon mirroring where direction carries meaning
- [ ] Fix bidirectional text handling where Arabic and Latin mix
- [ ] Fix any Arabic typography issues: line height, font weight, truncation

### Performance
- [ ] Add the indexes the query plans call for
- [ ] Fix N+1 queries on list screens
- [ ] Add pagination anywhere it is missing
- [ ] Cache what is safely cacheable
- [ ] Generate and serve image thumbnails rather than full photos in lists
- [ ] Reduce mobile sync payloads where data is being sent unnecessarily
- [ ] Reduce mobile bundle size and cold start time

### Security
- [ ] Fix every missing permission or scope check
- [ ] Close any RLS or isolation coverage gaps
- [ ] Tighten file upload validation and URL scoping
- [ ] Complete rate limiting coverage
- [ ] Update vulnerable dependencies
- [ ] Scrub any customer data found in logs or error reports
- [ ] Confirm security headers and CSP are complete

### Quality
- [ ] Add missing loading, empty and error states
- [ ] Make error messages actionable and translated
- [ ] Add retry affordances where an operation can fail transiently
- [ ] Fix accessibility findings

## Exit criteria

- [ ] Every agreed defect fixed
- [ ] Performance measurably improved against the recorded baseline
- [ ] No known security findings outstanding
