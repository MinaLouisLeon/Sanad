# 09 — Security & Compliance

## 1. Threat model

| Threat | Impact | Control |
|---|---|---|
| Cross-tenant data access | Catastrophic, existential | RLS forced in DB + automated isolation suite in CI |
| Broken object-level authorization (IDOR) | Severe | Scope checks in the service layer; UUIDs; automated per-endpoint tests |
| Stolen or lost engineer device | Moderate–severe | Encrypted local DB, biometric lock, scoped local data, remote wipe |
| Credential stuffing | Severe | Rate limiting, breached-password checks, MFA, anomaly alerts |
| Malicious file upload | Moderate | Type/size validation, virus scan, no execution, served from a separate origin |
| Insider / support abuse | Severe | Impersonation controls, immutable audit log, tenant-visible access records |
| Supply-chain compromise | Severe | Lockfiles, Dependabot, SCA in CI, provenance on builds |
| Payment/e-invoice tampering | Severe | Server-side verification, signed webhooks, reconciliation |
| Data loss | Existential | PITR backups plus quarterly tested restores |

## 2. Application security baseline

- **Authentication**: Argon2id password hashing, breached-password rejection at
  set/change time, TOTP MFA, phone OTP with per-number rate limits.
- **Sessions**: short access tokens, rotating refresh tokens with reuse
  detection (a replayed refresh token revokes the whole family), device-bound,
  individually revocable.
- **Authorization**: every service method re-checks capability and scope on the
  server. Client-side checks only hide UI.
- **Input validation**: Zod at every boundary; parameterized queries only.
- **Output**: React escaping by default; no `dangerouslySetInnerHTML` on
  user-supplied content without sanitization; strict CSP.
- **Headers**: HSTS with preload, CSP, X-Content-Type-Options, Referrer-Policy,
  Permissions-Policy.
- **CORS**: explicit allowlist; no wildcard on authenticated endpoints.
- **Rate limiting**: per IP, per user, per tenant, per endpoint class. Auth
  endpoints are strictest. Tenant-level quotas prevent one customer degrading
  another.
- **Secrets**: environment-injected, never in the repository; rotation runbook;
  automated secret scanning in CI and on push.
- **File uploads**: presigned direct-to-storage, content-type and magic-byte
  validation, size caps, virus scanning before the file becomes readable, served
  from a separate domain with short-lived signed URLs.

## 3. Audit log

Append-only, and it is a product feature as much as a control — customers in
regulated industries buy on it.

Recorded: actor, `impersonated_by` when applicable, action, entity type and id,
before/after diff, IP, user agent, request id, source (web/mobile/api/system),
timestamp.

Always audited: authentication events, permission and role changes, membership
changes, job status transitions, form publishes, submission verification and
amendment, stock adjustments and counts, cost and price changes, exports,
impersonation, billing changes, deletions.

The application database role has `INSERT` and `SELECT` on `audit_log` and no
`UPDATE` or `DELETE`. Retention: 24 months hot, then archived to cold storage;
enterprise plans can extend it.

Tenants see their own audit log in-app, filterable and exportable. This converts a
compliance obligation into a selling point.

## 4. Data protection and privacy

- **Encryption**: TLS 1.3 in transit; AES-256 at rest for database, backups and
  object storage; encrypted SQLite on devices.
- **PII inventory**: a maintained register of which tables hold personal data,
  which is what makes deletion and subject-access requests answerable rather
  than archaeological.
- **Data subject rights**: export (self-service), rectification (in-app),
  erasure (documented process with legal-hold exceptions).
- **Erasure vs anonymization**: where a record must survive for contractual or
  regulatory reasons — a signed inspection report, a stock movement — the person
  is anonymized (`Former employee #1423`) rather than the record deleted.
  Destroying a signed service record to satisfy a deletion request creates a
  different legal problem.
- **Retention policies** configurable per tenant within legal floors.
- **Sub-processor register** published: hosting, database, storage, email, SMS,
  WhatsApp, payments, error tracking, maps.
- **DPA** offered to every customer; SCCs where data leaves the region.

## 5. Regional and regulatory context

| Jurisdiction | Regime | Implication |
|---|---|---|
| Saudi Arabia | PDPL (SDAIA) | Consent and purpose limitation; localization expectations for some sectors |
| UAE | Federal PDPL + free-zone regimes (DIFC, ADGM) | DIFC/ADGM are separate regimes with their own requirements |
| Egypt | Law 151/2018 | Registration and licensing obligations for processors; local hosting preference |
| EU customers | GDPR | Applies to any EU-based user data |

**Data residency** is the sharpest constraint: Gulf government and some regulated
customers require in-country storage, and neither Vercel nor the managed Postgres
providers have a Saudi or UAE region today. The plan (`01-architecture.md` §8) is
a Frankfurt-hosted multi-tenant cloud plus a containerized single-tenant
deployment sold as an enterprise tier into AWS Bahrain or UAE. Keeping the Docker
build green in CI from day one is what makes that deal closeable rather than a
six-month project.

**E-invoicing** (ZATCA Phase 2 in KSA, ETA in Egypt) is a legal requirement, not
a feature — covered in `10-billing-and-plans.md` §6.

## 6. Compliance roadmap

- **Now:** written security policies, PII register, DPA template, sub-processor
  list, vulnerability disclosure contact, incident response runbook.
- **Pre-launch:** third-party penetration test; remediate high and critical
  findings before the first paying customer.
- **Year 1:** ISO 27001 if enterprise deals demand it (more recognized in the
  Gulf than SOC 2). SOC 2 Type II if selling to multinationals.
- **Continuous:** annual pen test, quarterly access review, quarterly restore
  drill, dependency scanning on every build.

## 7. Employee monitoring — handle deliberately

The product can capture GPS breadcrumbs, geofenced clock-in and timestamped
activity. This is legally and ethically sensitive, and mishandling it will
poison adoption with the exact users the product depends on.

Rules built into the product:
- Location tracking is **off by default**, enabled per tenant as an explicit
  choice.
- When enabled, the mobile app shows a persistent, unmissable indicator.
- Location is captured at **status transitions and form submission only** — no
  continuous background tracking in v1.
- Tracking never applies outside the engineer's working hours.
- Engineers can see their own recorded location data.
- Tenant admins must acknowledge their responsibility to inform staff, and the
  acknowledgement is recorded.

Continuous breadcrumb tracking, if ever built, is a separately-consented Phase 4
feature — not a default.

## 8. Incident response

Severity ladder from SEV1 (data breach, total outage, cross-tenant leak) to SEV4.
For SEV1 and SEV2: named incident commander, a status page updated within 30
minutes, customer notification within regulatory windows (72 hours under GDPR and
comparable regional rules), and a blameless post-mortem published to affected
customers within five working days.

The runbook is written and rehearsed **before** launch. Writing it during an
incident is how disclosure deadlines get missed.
