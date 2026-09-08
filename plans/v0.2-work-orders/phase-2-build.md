# v0.2 · Phase 2 — Build

## Tasks

### Domain — `packages/core`
- [ ] Customer, site, contact services
- [ ] Asset type and asset services, including tag and QR generation
- [ ] Job type service
- [ ] Job service with the status machine as an explicit, testable transition map
- [ ] Transition validation rejecting illegal moves rather than correcting them
- [ ] `job_status_history` written inside the same transaction as the transition
- [ ] Assignment service — one assignee in the UI, written to the `assignments` table

### Database
- [ ] Any tables the v0.1 full-DDL pass missed
- [ ] RLS policies and isolation coverage on every one of them
- [ ] Indexes for the job list and asset history queries
- [ ] Full-text search index with an Arabic configuration
- [ ] Seeds: a realistic company with customers, sites, assets and jobs, using
      **real Arabic names and addresses**, never placeholder text

### API
- [ ] CRUD routes for customers, contacts, sites, asset types, assets, job types, jobs
- [ ] Job assignment and status transition routes
- [ ] List routes with cursor pagination, filtering and sorting
- [ ] Search route
- [ ] Every route permission-checked server-side

### Web
- [ ] Customers: list, detail, create, edit — with sites and contacts inline
- [ ] Sites: list, detail, map location
- [ ] Assets: list, detail with job history, create, edit, printable QR label
- [ ] Job types: list and editor
- [ ] Jobs: list with filters, detail, create, edit
- [ ] Job detail: status actions, assignee, schedule, notes, history timeline
- [ ] Schedule view: jobs grouped by day, filterable by assignee
- [ ] Global search across customers, sites, assets and jobs
- [ ] Every string through i18n, in both catalogs

## Exit criteria

- [ ] A job can be created, assigned, scheduled and moved through every status
- [ ] Illegal transitions are refused with a clear message
- [ ] History shows every transition with actor and reason
- [ ] An asset shows its job history
- [ ] Search returns results in both languages
