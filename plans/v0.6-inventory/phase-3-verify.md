# v0.6 · Phase 3 — Verify

## Tasks

### Ledger integrity — the defining test of this version
- [ ] **Property test:** generate thousands of random movement sequences across
      items and locations, then assert `stock_levels` equals the ledger sum for
      every item and location combination
- [ ] Same assertion under concurrent writes to the same item and location
- [ ] Rebuilding the rollup from scratch reproduces it exactly
- [ ] A reversing movement restores the prior balance and both rows remain visible
- [ ] The runtime role cannot update or delete a movement — assert at the
      database level, not in application code

### Offline
- [ ] Consumption recorded offline arrives exactly once
- [ ] The same consumption replayed does not double-deduct
- [ ] Consumption exceeding the van balance is accepted, the balance goes
      negative, and the flag is raised
- [ ] Several engineers consuming the same item concurrently all succeed

### Isolation and access
- [ ] New tables and routes added to the isolation suite
- [ ] An engineer sees only their own van's stock, on web and on the device
- [ ] Confirm no cost or price field reaches the mobile sync payload

### Interface
- [ ] QA in English and Arabic RTL, web and mobile
- [ ] Numeric input on mobile uses a large keypad and is usable with gloves
- [ ] Extend `QA.md`

## Exit criteria

- [ ] Ledger property test green, including the concurrent case
- [ ] Rebuild proven to reproduce the rollup
- [ ] Ledger immutability enforced at the database level
- [ ] Offline consumption correct, including the negative-balance path
- [ ] Isolation extended and green
