# Architecture Decision Records

Each ADR records one significant decision: its context, the options considered,
the choice made, and its consequences. ADRs are immutable once accepted — a
changed decision is a new ADR that supersedes the old one.

| ADR | Decision | Status |
|---|---|---|
| [001](./001-mobile-platform.md) | Expo / React Native for the field app | Accepted |
| [002](./002-target-market.md) | MENA / Gulf market | Accepted — language policy superseded by 014 |
| [003](./003-hosting-and-platform.md) | Vercel + managed Postgres | Accepted |
| [004](./004-launch-scope.md) | All four pillars at launch | Accepted |
| [005](./005-offline-sync.md) | Offline sync approach | **Resolved by 015** — PowerSync adopted, spike cancelled |
| [006](./006-tenant-isolation.md) | Shared database with RLS | Accepted |
| [007](./007-form-versioning.md) | Immutable published form versions | Accepted |
| [008](./008-inventory-ledger.md) | Append-only stock movement ledger | Accepted |
| [009](./009-launch-market-and-entity.md) | UAE entity and launch market, Saudi as revenue target | Accepted |
| [010](./010-first-vertical.md) | HVAC/MEP contractors as the first vertical | Accepted |
| [011](./011-field-workforce-languages.md) | Office Arabic-first, field app English-first | **Superseded by 014** |
| [012](./012-approval-engine.md) | One small generic approval engine | Accepted |
| [013](./013-customer-portal-in-v1.md) | Read-only customer portal in v1 | **Superseded by 015** |
| [014](./014-language-support.md) | English default; English and Arabic only | Accepted |
| [015](./015-solo-part-time-delivery.md) | Solo part-time delivery; four pillars kept, depth cut | Accepted |
