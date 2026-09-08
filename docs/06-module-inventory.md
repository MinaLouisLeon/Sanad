# 06 — Inventory

## 1. The founding decision: stock is a ledger

Stock on hand is **never** a mutable column that gets incremented and
decremented. It is the sum of an append-only movement ledger.

```
quantity_on_hand(item, location) = Σ movements INTO − Σ movements OUT OF
```

`stock_levels` exists as a maintained rollup for query speed, but it is a cache.
If it ever disagrees with the ledger, the ledger is right and the cache is
rebuilt.

Why this is non-negotiable:
- Mutable counters make "why are we 40 valves short?" permanently unanswerable.
- Concurrent updates from a warehouse app and three vans corrupt counters;
  ledger inserts do not conflict.
- Auditors, and any customer with a real finance function, require movement
  history.
- Corrections become reversing entries — visible, attributable, reversible —
  instead of silent overwrites.

**Corrections are new movements, never edits.** A `reversal_of_id` links the
correcting entry to its original.

## 2. Locations, including vans

```
warehouse ──transfer──▶ van (engineer) ──consumption──▶ job/asset
    ▲                     │
    │                     └──return──▶ warehouse
    │
supplier ──receipt──┘                  scrap ◀──scrap──
```

Location types: `warehouse`, `van`, `site`, `supplier`, `customer`, `scrap`,
`in_transit`.

**Van stock is the feature that distinguishes field-service inventory from
generic inventory**, and generic systems get it wrong. Each engineer's vehicle is
a real stock location with a real balance. Consequences:

- An engineer sees what is on their own van, offline, before promising a fix.
- Consumption on a job deducts from the van, not from an abstract pool.
- Van replenishment: min/max levels per van per item, generating a pick list at
  the warehouse.
- Van stock counts are a fast weekly mobile task, scanned rather than typed.
- Transfers between vans (engineer to engineer on site) with a two-step
  confirmation, so stock in transit is never invisible.

## 3. Movement types

| Type | From | To | Trigger |
|---|---|---|---|
| `receipt` | supplier | warehouse | Goods received against a PO |
| `transfer` | any | any | Warehouse → van, van → van, warehouse → warehouse |
| `consumption` | van/warehouse | job (virtual) | Engineer uses a part |
| `return` | job/van | warehouse | Unused or removed part |
| `adjustment` | — | ± | Manual correction; reason mandatory |
| `count` | — | ± | Result of a stock count |
| `scrap` | any | scrap | Damaged or expired |

Every movement records: item, quantity, from, to, type, reference (job / PO /
count), serial or batch where applicable, unit cost, timestamp, actor, note.

## 4. Serials, batches and expiry

- **Serialized items** (compressors, boards, meters): each unit is an individual
  record with its own status and location, and — critically — the asset it was
  installed into. That link is what makes warranty claims and failure analysis
  possible.
- **Batch/lot tracked items** (refrigerant, sealant, filters): batch number,
  received date, expiry date. FEFO (first-expired-first-out) picking suggestions;
  expired batches blocked from consumption with an override that is audited.
- Expiry alerts at configurable thresholds, per location — including vans, where
  expired stock quietly accumulates.

## 5. Reservations

When a job is scheduled with planned parts, those parts are **reserved**, not
deducted:

```
quantity_available = quantity_on_hand − quantity_reserved
```

Reservation releases on consumption, cancellation, or expiry of the reservation
window. Without this, two dispatchers promise the same last compressor to two
customers on the same morning.

## 6. Procurement

Deliberately lightweight in v1 — this is not an ERP.

- Reorder points per item per location; a background job raises suggested
  purchase requisitions.
- Purchase orders: supplier, lines, expected date, approval by threshold.
- Partial receipts supported (receive 8 of 10, PO stays open).
- Receiving on mobile by scanning.
- Landed cost and average cost maintained per item per location.
- **Out of scope for v1:** supplier price lists, RFQ workflows, three-way
  matching, multi-currency purchasing. These belong to accounting systems we
  integrate with in Phase 3.

## 7. Costing

- **Weighted average cost** per item per location, recalculated on receipt.
  Chosen over FIFO/LIFO because it is simple, defensible, and sufficient for
  service parts. FIFO is a Phase 4 option for tenants who need it.
- Consumption posts at the moving average cost at the moment of consumption, and
  that figure is frozen onto the job. A later price change must never
  retroactively alter a closed job's cost.
- Cost visibility is permission-gated: engineers see quantities, never costs.

## 8. Stock counts

- Full counts and cycle counts (rolling subsets by category, value or movement
  frequency).
- Mobile-first, scanner-driven, blind by default (the counter does not see the
  expected quantity — otherwise they confirm the system instead of counting).
- Variance report requiring approval above a configurable threshold.
- Approval posts `count` movements; the ledger absorbs the correction with full
  attribution.

## 9. Barcode and QR

- Scan to identify an item, to count, to consume, to receive, to transfer.
- Supports EAN/UPC, Code128, QR, Data Matrix.
- Sanad generates internal labels for items without manufacturer barcodes,
  printable as sheets.
- Camera scanning in Expo works offline against the locally synced item catalog —
  essential in a basement plant room with no signal.

## 10. Reporting

Stock on hand by location; stock value; movement history per item; consumption by
job type, customer and engineer; parts cost per job; dead stock (no movement in
N days); shrinkage from count variances; expiry exposure; van stock accuracy per
engineer; reorder exposure.

"Van stock accuracy per engineer" is quietly one of the most valuable reports in
the product — it turns an invisible loss into a managed metric.

## 11. Interaction with offline mobile

- The item catalog and the engineer's own van balances sync to the device.
- Consumption recorded offline is queued as a movement with a client-generated
  UUID and replayed on reconnect, idempotently.
- **Negative van balances are permitted after sync.** An engineer offline in a
  basement cannot be blocked from recording reality. The system accepts the
  movement, flags the negative balance for the inventory manager, and prompts
  reconciliation. Refusing the entry would simply push the data back onto paper,
  which is the outcome the entire product exists to prevent.
