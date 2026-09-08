# v0.6 — Inventory

**Goal:** parts flow from warehouse to van to job, and the ledger always explains
where everything went.

## Ships with

- Items and categories
- Stock locations: warehouse and **van** — each engineer's vehicle is a real
  location with a real balance
- **Append-only stock movement ledger** (ADR-008)
- `stock_levels` rollup, derived and rebuildable
- Transfers: warehouse to van, van to van
- Consume-on-job from the phone, deducting from the engineer's own van
- Returns and adjustments
- Stock list, movement history, per-van balances

## Does not ship with

Serial numbers, batches and expiry, reservations, purchase orders, suppliers,
stock counts, average costing, reorder points, barcode scanning. All ADR-015
depth cuts. Per "model full, build thin", the `serials`, `batches`,
`stock_counts` and `purchase_orders` tables are still created.

## The rule that governs this version

**Stock on hand is never a mutable column.** It is the sum of the ledger.
`stock_levels` is a cache; if it disagrees with the movements, the movements win
and the cache is rebuilt. Corrections are reversing entries, never edits.

## Exit criteria

- [ ] An item can be created and stocked into a warehouse
- [ ] Stock transfers to a van and the van balance reflects it
- [ ] An engineer consumes a part on a job from the phone, offline, and the
      ledger records it on sync
- [ ] The rollup always equals the ledger sum, proven by a property test
- [ ] Rebuilding the rollup from scratch reproduces it exactly
- [ ] A correction posts a reversing movement; no history is edited
- [ ] Offline consumption exceeding the van balance is **accepted** and flagged,
      not rejected

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
