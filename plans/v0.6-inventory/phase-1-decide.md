# v0.6 · Phase 1 — Decide

## Inputs

- `docs/06-module-inventory.md`
- ADR-008 (append-only ledger)

## Tasks

### Ledger
- [ ] Confirm the movement types shipping in v0.6: receipt, transfer, consumption,
      return, adjustment, scrap
- [ ] Decide the ledger row shape: item, quantity, from, to, type, reference,
      actor, timestamp, note
- [ ] Decide the sign convention and how a transfer is represented — one row with
      from and to, not two rows
- [ ] Decide how corrections work: a reversing movement linked by `reversal_of_id`
- [ ] Decide database-level protection against updating or deleting a movement

### Rollup
- [ ] Decide how `stock_levels` is maintained — trigger, or application write in
      the same transaction as the movement
- [ ] Decide the rebuild command and when it is run
- [ ] Decide how concurrent movements to the same item and location are handled

### Locations
- [ ] Decide how a van is linked to an engineer, and what happens when they leave
      or swap vehicles
- [ ] Decide whether a job is a virtual location or a movement reference
      (recommendation: a reference, so the ledger keeps two real endpoints)
- [ ] Decide who may transfer, and between which location types

### Offline consumption — the important one
- [ ] Confirm the decision from `docs/06-module-inventory.md` §11: an offline
      consumption that would drive a van balance negative is **accepted**. The
      movement posts, the negative balance is flagged for the inventory manager,
      and reconciliation is prompted
- [ ] Decide how a negative balance is surfaced, and to whom
- [ ] Decide what the engineer sees, if anything — they should not be blocked
      mid-job over a stock record

### Mobile
- [ ] Decide what syncs to a device: the item catalogue and that engineer's own
      van balances only
- [ ] Decide the consume flow: pick from van stock, set quantity, attach to the job
- [ ] Decide the return flow

## Exit criteria

- [ ] Movement types and ledger shape fixed
- [ ] Rollup maintenance and rebuild decided
- [ ] Negative-balance policy confirmed and written down
- [ ] Mobile sync scope for inventory decided
