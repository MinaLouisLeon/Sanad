# v0.6 · Phase 2 — Build

## Tasks

### Database
- [ ] `item_categories`, `items`, `stock_locations`, `stock_movements`,
      `stock_levels`, `job_parts`
- [ ] Create `serials`, `batches`, `stock_counts`, `purchase_orders` and their
      lines too — model full, build thin (ADR-015)
- [ ] RLS policies and isolation coverage on all of them
- [ ] Revoke `UPDATE` and `DELETE` on `stock_movements` from the runtime role.
      Application discipline is not enough for the project's central ledger
- [ ] Index `(tenant_id, item_id, location_id, occurred_at desc)`

### Domain
- [ ] Item service: create, list, categorise
- [ ] Location service, including van assignment to an engineer
- [ ] **Movement service** — the only path that writes stock. Every operation
      below goes through it
- [ ] Rollup maintenance inside the same transaction as the movement
- [ ] Rollup rebuild command
- [ ] Transfer, consumption, return, adjustment, scrap operations
- [ ] Negative-balance detection and flagging
- [ ] Job parts: link consumption to a job and to its asset

### Web
- [ ] Items: list, detail, create, edit
- [ ] Locations: list, van assignment
- [ ] Stock on hand by location, with a search
- [ ] Movement history per item and per location, with filters
- [ ] Transfer screen: warehouse to van, van to van
- [ ] Adjustment screen, with a mandatory reason
- [ ] Negative balance alert list for the inventory manager
- [ ] Parts used, shown on the job detail screen

### Mobile
- [ ] Item catalogue and own van balances added to the sync scope
- [ ] My Van: current balances, searchable, works offline
- [ ] Consume a part on a job: pick, quantity, confirm
- [ ] Return a part
- [ ] Consumption queued as a mutation and replayed idempotently on sync

## Exit criteria

- [ ] Stock receives into a warehouse, transfers to a van, and is consumed on a job
- [ ] Every one of those appears in the ledger with correct endpoints
- [ ] The rollup matches the ledger after each operation
- [ ] An offline consumption syncs and appears against the job
