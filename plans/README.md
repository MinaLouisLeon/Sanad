# Sanad — Build Plans

How Sanad gets built, from an empty repository to a system a real company uses.

**No dates, no durations, no estimates anywhere in this folder.** Work is
sequenced, not scheduled. A version is done when its exit criteria are met.

## Approach: versions, each with phases

Nine versions. Each one is a **vertical slice that works end to end** and is
deployed to production before the next begins — not a horizontal layer.

Production is therefore not a destination at the end of this plan. It is entered
at v0.1 and every version after is an increment on a live system. Migrations,
backups, RLS and deploys get exercised repeatedly rather than invented once,
under pressure, at the end.

Each version leaves the project in a state where stopping is not a failure.

## The version ladder

| Version | What exists when it ships |
|---|---|
| [v0.1](./v0.1-platform-foundation/) · Platform foundation | Multi-tenant, auth, roles, audit log, English/Arabic, deployed. A company can be created and users invited |
| [v0.2](./v0.2-work-orders/) · Work orders | Customers, sites, assets, jobs, statuses, assignment. Replaces a spreadsheet |
| [v0.3](./v0.3-forms-on-jobs/) · Forms on jobs | Versioned forms rendered on the web and attached to jobs. A job carries a completed form |
| [v0.4](./v0.4-mobile-field-app/) · Mobile field app | Expo app with offline sync, photos, signature. First version a field engineer can use |
| [v0.5](./v0.5-form-builder-and-pdf/) · Form builder + PDF | Admins build forms with no code; branded PDF output in both languages |
| [v0.6](./v0.6-inventory/) · Inventory | Movement ledger, warehouse and van stock, consume-on-job from the phone |
| [v0.7](./v0.7-meetings-and-notifications/) · Meetings + notifications | Meetings, minutes, action items; in-app, push and email delivery |
| [v0.8](./v0.8-arabic-and-hardening/) · Arabic + hardening | Full RTL pass, offline scenario suite, security and performance review |
| [v1.0](./v1.0-production-release/) · Production release | Onboarding, verified backups, monitoring, runbooks. A real company can sign up |

All four pillars (ADR-004) are present by v0.7. The ladder controls **order**,
never whether something ships.

## The four phases inside every version

Same rhythm every time.

```
Phase 1  DECIDE    Schema changes, contracts and decisions — written down first
Phase 2  BUILD     Database → domain → API → UI
Phase 3  VERIFY    Automated tests, tenant isolation, manual QA in English and Arabic
Phase 4  RELEASE   Migrate, deploy, tag, release note, update docs
```

Phase 1 exists because the dominant risk on this project is **context loss
between sessions**, not engineering difficulty. Returning after a long gap to a
written decision is cheap; returning to half-written code is not.

Phase 4 is never skipped, even when nothing user-visible changed. The release
ritual is what keeps production deployable.

## Rules that hold across every version

1. **Tenant isolation is never deferred.** Every new table gets `tenant_id`, an
   RLS policy, and isolation test coverage in the same phase it is created.
2. **Model full, build thin** (ADR-015). Create tables as specified in
   `docs/03-data-model.md`, even where this version's UI only handles the simple
   case. Adding a column later is trivial; reshaping a populated table is not.
3. **Published form versions are immutable** from the first commit that creates
   them (ADR-007). This cannot be retrofitted.
4. **Stock is an append-only ledger** (ADR-008). No mutable quantity column, ever.
5. **English and Arabic ship together.** A key added in `en` without `ar` fails
   the build. RTL is reviewed before merge, not before release.
6. **Never leave the repository broken.** Commit working states only.
7. **Every version ends deployed.** A version that exists only on your machine is
   not done.

## Working notes

Each version folder holds a `NOTES.md` after work begins — what was done, what is
half-finished, what is next. Written at the end of every session. Ten minutes
there saves an hour of re-reading code three weeks later.

## Related documents

- `docs/` — the full specification set: architecture, data model, module specs
- `docs/adr/` — decisions and their reasoning
- `docs/15-roadmap.md` — delivery model and scope depth (ADR-015)
