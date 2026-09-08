# ADR-003 — Vercel + managed Postgres

**Status:** Accepted · 2026-09-08

## Context
The team is small, the stack is Next.js, and time to production matters more
than infrastructure control at this stage.

## Decision
Next.js on Vercel, managed Postgres (Supabase or Neon) in `eu-central-1`,
S3-compatible object storage, Inngest for durable background workflows, and a
separately deployed containerized service for PDF rendering.

## Consequences
- Minimal operational burden; preview deploys per pull request.
- **No Gulf region is available** from these providers, so Gulf latency is
  90–130 ms and in-country residency is impossible on this platform.
- Therefore: the application must remain fully containerizable, with a Docker
  build kept green in CI from day one, so a single-tenant deployment into AWS
  Bahrain or UAE can be sold as an enterprise tier without a rewrite.
- Domain code must not depend on Vercel-only primitives.
- PDF rendering runs outside Vercel because serverless execution limits make
  headless Chromium fragile there.
