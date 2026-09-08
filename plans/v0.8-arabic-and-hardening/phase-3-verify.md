# v0.8 · Phase 3 — Verify

## Tasks

### Arabic
- [ ] Second full pass over every screen in Arabic, web and mobile, on devices
- [ ] Terminology consistency verified across every surface
- [ ] Have a native Arabic speaker read the product — not review a spreadsheet of
      strings, but use the application. The defects that matter are the ones a
      translator cannot see

### Offline suite — the release gate
- [ ] Airplane-mode day, including both force-kill variants
- [ ] Flaky network at 30% loss
- [ ] High latency
- [ ] 72-hour accumulation
- [ ] Conflict scenarios
- [ ] Clock skew
- [ ] Run on every device in the matrix

### Performance
- [ ] Load test at a realistic single-tenant profile, sustained
- [ ] Confirm p95 latency meets the targets in `docs/01-architecture.md` §9
- [ ] Soak test to expose leaks and connection exhaustion
- [ ] Mobile sync timing on a throttled connection

### Security
- [ ] Full isolation suite green
- [ ] Manual attempt to access another tenant's data through every entry point
      you can think of, including file URLs and error messages
- [ ] Authentication and session handling reviewed
- [ ] Dependency scan clean

### Regression
- [ ] Full `QA.md`, every section, both languages
- [ ] Every automated test green

## Exit criteria

- [ ] Arabic verified by a native speaker using the product
- [ ] Offline suite green across the device matrix
- [ ] Performance targets met
- [ ] Security pass clean
- [ ] Full regression green
