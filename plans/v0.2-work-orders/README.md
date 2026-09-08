# v0.2 — Work Orders

**Goal:** an administrator can record and track real field work on the web.

This is the first version with business value. On its own it already replaces the
spreadsheet-and-WhatsApp arrangement most target customers run on.

## Ships with

- Customers, contacts and sites
- Asset types and assets, with a QR code per asset
- Job types and jobs
- The job status machine with full transition history
- Single assignee and a scheduled date
- Job list with filters, and a job detail screen
- A simple day and week schedule view — a list, not a dispatch board

## Does not ship with

Forms, mobile app, inventory, meetings, notifications. Also deliberately absent:
drag-and-drop dispatch, multiple assignees, tasks within jobs, recurring jobs,
SLA timers, time tracking and costing. Those are ADR-015 depth cuts and arrive
after v1.0.

Per ADR-015 the `tasks` and `assignments` tables are still **created** in this
version even though the UI only handles one assignee.

## Exit criteria

- [ ] A customer, a site and an asset can be created and linked
- [ ] A job can be created against a customer, site and asset, assigned and scheduled
- [ ] Job status moves through the machine, and invalid transitions are rejected
- [ ] Every transition is recorded with actor, timestamp and reason
- [ ] The job list filters by status, assignee, customer and date
- [ ] An asset shows its own job history
- [ ] Isolation coverage extended to every new table and route
- [ ] Every new screen reviewed in Arabic RTL

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
