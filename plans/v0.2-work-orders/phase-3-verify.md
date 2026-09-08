# v0.2 · Phase 3 — Verify

## Tasks

### Isolation — extend, never assume
- [ ] Add every new table to the isolation suite
- [ ] Add every new route to the isolation suite
- [ ] Confirm search cannot return another tenant's records — a classic leak path
- [ ] Confirm filters and counts cannot leak the existence of other tenants' rows

### Domain
- [ ] Status machine: unit test every legal transition and a representative set
      of illegal ones
- [ ] Reason-required transitions refuse to proceed without one
- [ ] History rows written exactly once per transition
- [ ] Job numbering: unique per tenant, and no gaps caused by failed inserts
- [ ] Concurrent transitions on one job resolve safely

### RBAC
- [ ] An engineer cannot create or reassign jobs
- [ ] A dispatcher can assign but cannot delete customers
- [ ] Every new permission tested allowed and denied

### Interface
- [ ] Manual QA of every new screen in English
- [ ] Manual QA of every new screen in **Arabic RTL** — tables, filters, the
      timeline, and date fields are where RTL breaks first
- [ ] Long Arabic customer and site names do not break list layouts
- [ ] Empty, loading and error states present on every screen
- [ ] Extend `QA.md` with the v0.2 checks

### Performance
- [ ] Seed several thousand jobs and confirm the list and filters stay fast
- [ ] Check query plans use the intended indexes

## Exit criteria

- [ ] Isolation suite extended and green
- [ ] Status machine fully covered by tests
- [ ] Arabic RTL pass complete on all new screens
- [ ] Job list performs acceptably at realistic volume
