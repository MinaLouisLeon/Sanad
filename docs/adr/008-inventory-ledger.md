# ADR-008 — Inventory as an append-only movement ledger

**Status:** Accepted · 2026-09-08

## Context
Stock is written concurrently from a warehouse, several vans and a mobile app
that is frequently offline. Customers and auditors need to explain discrepancies.

## Decision
`stock_movements` is append-only and is the sole source of truth. Quantity on
hand is derived; `stock_levels` is a maintained rollup treated purely as a cache.
Corrections are posted as reversing movements linked by `reversal_of_id`.
Movements are never updated or deleted.

## Consequences
- Every discrepancy is explainable, attributable and reversible.
- Concurrent writes append rather than contend, which matters with many vans
  syncing at once.
- The rollup is fully rebuildable; if the two disagree, the ledger wins.
- A property test asserts rollup equals ledger under randomized concurrent
  sequences (`14-testing-and-quality.md` §5).
- Offline consumption may drive a van balance negative. This is **accepted**: the
  movement posts, the negative balance is flagged for reconciliation, and reality
  is never rejected in favour of the ledger's expectation.
