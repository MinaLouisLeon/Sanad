# 14 — Testing & Quality Strategy

## 1. Shape of the suite

```
        ╱  E2E (Playwright + Detox)      ~5%   critical journeys only
      ╱    Integration (API + DB + RLS)  ~30%  the highest-value layer
    ╱      Unit (domain logic)           ~50%  pure, fast
  ╱        Static (TS, lint, schema)     ~15%  free, instant
```

Integration tests are weighted unusually high because the two things most likely
to cause an existential incident — **tenant isolation** and **offline sync
correctness** — cannot be verified by unit tests. They are properties of the
database and the protocol, not of a function.

## 2. Tenant isolation suite — mandatory, blocking

The single most important test asset in the product.

For every tenant-scoped table and every API endpoint:

1. Seed two tenants, A and B, each with users, jobs, forms, submissions, stock
   and files.
2. Authenticate as a user of tenant A.
3. Assert A receives **exactly zero** of B's rows through: direct fetch by B's id,
   list endpoints, search, filters, aggregate counts, exports, report queries,
   webhook payloads, and file URLs.
4. Assert that a write referencing B's id fails.
5. Assert error messages do not leak existence (a 404, never a 403 that confirms
   the record exists).

Additional cases: a user with no tenant context receives nothing; branch-scoped
users cannot read other branches; API keys are confined to their tenant; a
background job without tenant context fails loudly rather than reading globally;
an impersonation session is confined to its target tenant.

**Enforcement:** a CI check enumerates tables and routes and fails the build when
one lacks isolation coverage. A developer cannot ship a new endpoint without it.

## 3. Offline and sync testing

Covered in `08-mobile-and-offline.md` §8. The blocking gate before every mobile
release is the **airplane-mode scenario suite**: a full day of work completed
with no connectivity — jobs started and completed, forms with photos and
signatures, parts consumed, time recorded — then reconnected, asserting every
artefact arrives exactly once, in order, with no data loss and no duplicates.

Also required: flaky-network simulation, kill-during-sync, 72-hour offline
accumulation, clock-skew handling, and conflict scenarios producing correct
inbox entries rather than silent discards.

## 4. Forms engine testing

The conditional-logic evaluator, validation rules and scoring calculator are
implemented **once** in `packages/forms-engine` and shared by web, mobile and
server. A property-based test suite runs generated schemas and answer sets
through the evaluator, asserting the same result in all three environments.

Divergence between the mobile renderer's view of "is this field required" and the
server's is a bug class that produces rejected submissions in the field, hours of
support time, and engineers who stop trusting the app.

Also tested: version immutability (publishing never mutates a prior version),
rendering a five-year-old submission against its own version, migration of drafts
across publishes, and PDF output with Arabic content and RTL layout.

## 5. Ledger integrity testing

For inventory: a property test that generates thousands of random movement
sequences across locations, then asserts that the `stock_levels` rollup equals
the ledger sum for every item and location, including under concurrent writes,
reversals, negative-balance scenarios and rebuilds from scratch.

## 6. E2E journeys (Playwright, web)

Kept deliberately few and genuinely critical:

1. Sign up → onboarding → first job created → assigned.
2. Admin builds a form, publishes it, attaches it to a job type.
3. Dispatcher schedules a week of jobs and reassigns one, with conflict detection.
4. Supervisor verifies a completed job with a submitted form, exports the PDF.
5. Inventory manager transfers stock to a van, runs a count, approves variance.
6. Meeting created → attended → minutes → action item converted to a job.
7. Billing: trial → subscribe → seat change → invoice issued.

Each journey is run in **both Arabic (RTL) and English (LTR)**. RTL is not a
cosmetic variant here — layout, drag interactions on the dispatch board and PDF
rendering all break differently in RTL, and only an actual run catches it.

## 7. Non-functional testing

- **Load**: a realistic tenant profile (200 users, 5,000 jobs/month, 50 concurrent
  dispatchers, 200 mobile devices syncing) sustained, with p95 tracked against
  targets.
- **Soak**: 24 hours to expose leaks and connection exhaustion.
- **Spike**: Sunday 07:00 Gulf time, when every engineer in every tenant opens
  the app within fifteen minutes. This is the real peak and it is highly
  correlated across tenants.
- **Security**: SAST and dependency scanning in CI, secret scanning, quarterly
  DAST, annual third-party penetration test.
- **Accessibility**: WCAG 2.1 AA on web; keyboard navigation on the dispatch
  board; screen reader support; contrast verified in both themes.
- **Device matrix** for mobile as specified in `08-mobile-and-offline.md` §5,
  weighted towards low-end Android because that is the real field population.

## 8. Quality gates

**Cannot merge:** typecheck or lint failure; any test failure; isolation suite
failure or missing coverage for a new endpoint; new table without `tenant_id` and
an RLS policy; unreviewed migration; coverage drop below threshold on the domain
layer; a hard-coded string in a user-facing surface that bypasses i18n.

**Cannot release to production:** E2E green in both locales; no open SEV1/SEV2;
migration reviewed for lock behaviour; rollback plan stated; staging soak passed.

**Cannot release mobile:** airplane-mode suite green; device matrix passed; OTA
rollback verified; minimum-supported-version floor updated if the sync protocol
changed.

## 9. Test data

A seed generator producing a realistic tenant: an Arabic-named contracting
company with three branches, 40 engineers, 300 customers, 900 sites, 4,000
assets, 12 form templates, 20,000 historical jobs and submissions, and an item
catalogue with van stock.

Realistic **Arabic content is used throughout**, not Latin placeholder text.
Layout bugs, truncation, sorting and PDF shaping failures are invisible against
lorem ipsum and obvious against real Arabic names and addresses.
