# ADR-005 — Offline synchronization approach

**Status:** **Resolved by [ADR-015](./015-solo-part-time-delivery.md)** — PowerSync
adopted directly; the spike is cancelled.

> A two-week spike that might end in hand-writing a sync engine is a risk a
> solo part-time project cannot absorb. Option A is taken on the reasoning below
> without evaluating Option B. The consequences at the end of this ADR — UUIDv7
> ids, idempotency keys, the conflict matrix, the airplane-mode release gate —
> apply unchanged.
>
> **Original status:** Proposed — to be resolved by a two-week spike in M0

## Context
Bidirectional sync between Postgres and mobile SQLite is the highest-risk
component in the product. Failure here means engineers revert to paper.

## Options
**A — PowerSync (managed).** Handles streaming replication, hydration,
reconnection and partial replication by sync rules. Saves an estimated 6–10
engineering weeks. Costs a per-connection fee, constrains some schema choices,
and introduces a vendor dependency on a critical path.

**B — Custom mutation queue.** Local SQLite mirror plus an append-only mutation
outbox with client-generated UUIDs, replayed with idempotency keys. Full control
and no vendor, but reconnection, ordering and partial-failure edge cases are
where field apps fail.

## Decision (pending)
Spike PowerSync for two weeks in M0 against a realistic slice: a day of jobs,
a form submission with photos, and a stock consumption, exercised across offline,
flaky and reconnecting networks. Adopt if it holds; otherwise implement Option B
using what the spike established.

## Consequences regardless of outcome
- Entity IDs are client-generated **UUIDv7** from day one.
- All mutations carry idempotency keys.
- The conflict-resolution matrix in `08-mobile-and-offline.md` §4 applies either
  way.
- The airplane-mode scenario suite is a blocking release gate.
