# v0.8 — Arabic & Hardening

**Goal:** no new features. Make what exists correct, fast, secure and genuinely
bilingual.

Every version so far shipped with an Arabic RTL review, so this is not a rescue
mission. It is the pass that catches what accumulates across six versions: the
screen nobody revisited, the query that got slow at volume, the dependency that
went stale, the error state that was never designed.

Feature work is out of scope. If something is missing, it belongs to a version
after v1.0.

## Ships with

- Full Arabic RTL audit of every screen, web and mobile, on real devices
- Arabic terminology from `docs/17-terminology-ar.md` applied consistently
- Complete offline scenario suite, run as a release gate
- Performance and index tuning at realistic data volume
- Security pass and dependency audit
- Error handling, empty states and loading states everywhere
- Accessibility pass on the web

## Exit criteria

- [ ] Every screen reviewed in Arabic on a real device, not in a browser resize
- [ ] Terminology consistent across UI, notifications and PDFs
- [ ] Offline scenario suite green
- [ ] p95 latency within the targets in `docs/01-architecture.md` §9
- [ ] No high or critical findings outstanding from the security pass
- [ ] Every screen has designed loading, empty and error states
- [ ] Dependencies current, no known vulnerabilities

## Phases

1. [Decide](./phase-1-decide.md)
2. [Build](./phase-2-build.md)
3. [Verify](./phase-3-verify.md)
4. [Release](./phase-4-release.md)
