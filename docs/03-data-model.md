# 03 — Data Model

Illustrative, not final DDL. Column lists show intent and the decisions that
matter; exact types and indexes are settled during implementation.

## 1. Conventions applied to every table

| Convention | Reason |
|---|---|
| `id uuid` primary key, **UUIDv7**, client-generatable | Offline creation without a server round trip; time-ordered so index locality is preserved |
| `tenant_id uuid not null` on all tenant-scoped tables | RLS predicate |
| `branch_id uuid` where operationally scoped | Second-level RLS and dispatch filtering |
| `created_at`, `updated_at` (timestamptz, UTC) | Always UTC in storage; rendered in tenant timezone |
| `created_by`, `updated_by` | Attribution |
| `deleted_at` soft delete on user-visible entities | Recoverable mistakes; excluded by default views |
| `version integer` on concurrently edited rows | Optimistic locking; rejects stale offline writes |
| Money as `numeric(18,4)` + `currency char(3)` | Never floats |
| Enumerations as Postgres enums or lookup tables | Not free strings |
| Every index leads with `tenant_id` | RLS predicate is always present, so it must be the leading column |

## 2. Platform & tenancy

```
tenants
  id, name, legal_name, slug (unique), country_code, timezone,
  default_locale, currency, weekend_days int[] (e.g. {5,6} Fri–Sat),
  fiscal_year_start_month, tax_registration_number,
  status (trial|active|past_due|suspended|cancelled),
  plan_id, trial_ends_at, logo_url, brand_color, settings jsonb

branches
  id, tenant_id, name, code, address jsonb, geo point, timezone (override),
  working_hours jsonb, holiday_calendar_id, is_default, status

teams              id, tenant_id, branch_id, name, supervisor_user_id
holiday_calendars  id, tenant_id, name, country_code
holidays           id, calendar_id, date, name, is_recurring_hijri
plans              id, code, name, price_monthly, price_annual, currency,
                   entitlements jsonb, is_public
feature_flags      id, tenant_id (nullable = global), key, enabled, rollout_pct
```

`weekend_days` and `holiday_calendars` are core scheduling inputs, not
cosmetics — see `11-notifications-and-i18n.md` §5.

## 3. Identity & access

```
users              id, email (citext unique), phone_e164, password_hash,
                   full_name, avatar_url, locale, mfa_secret, mfa_enabled,
                   last_login_at, status
memberships        id, tenant_id, user_id, role_id, branch_ids uuid[],
                   team_ids uuid[], employee_code, job_title,
                   status (invited|active|suspended|offboarded),
                   UNIQUE (tenant_id, user_id)
roles              id, tenant_id (null = system role), key, name,
                   permissions text[], is_system
invitations        id, tenant_id, email, role_id, token_hash, expires_at,
                   invited_by, accepted_at
sessions           id, user_id, tenant_id, device_id, device_name, platform,
                   refresh_token_hash, ip, user_agent, expires_at, revoked_at
platform_admins    id, email, password_hash, mfa_secret (mandatory), role
impersonations     id, platform_admin_id, tenant_id, user_id, reason,
                   ticket_ref, mode (read|write), approved_by,
                   started_at, ends_at, ended_at
```

Engineer skills and certifications drive dispatch eligibility:

```
skills             id, tenant_id, name, category
user_skills        user_id, skill_id, level
certifications     id, tenant_id, user_id, type, number, issued_at,
                   expires_at, document_url, verified_by
```

`certifications.expires_at` is enforced at assignment time — dispatching an
engineer whose gas or high-voltage certification has lapsed is a compliance
incident, so the system blocks it and explains why.

## 4. Customers, sites, assets

```
customers   id, tenant_id, name, code, type (individual|company),
            tax_number, billing_address jsonb, payment_terms_days,
            credit_limit, account_manager_id, status, tags text[]
contacts    id, tenant_id, customer_id, site_id, name, role, phone_e164,
            whatsapp_e164, email, is_primary, preferred_locale
sites       id, tenant_id, customer_id, name, code, address jsonb,
            geo point, access_notes, access_hours jsonb, timezone,
            parent_site_id (buildings→floors→rooms)
contracts   id, tenant_id, customer_id, code, type (amc|warranty|t&m),
            start_date, end_date, sla_policy_id, covered_site_ids uuid[],
            covered_asset_ids uuid[], value, billing_frequency, status

asset_types id, tenant_id, name, category, manufacturer, model,
            spec_schema jsonb, default_form_ids uuid[],
            default_pm_schedule jsonb
assets      id, tenant_id, customer_id, site_id, asset_type_id,
            tag (unique per tenant), qr_code, serial_number, name,
            location_description, installed_at, warranty_expires_at,
            parent_asset_id, criticality (low|medium|high|critical),
            status (active|faulty|under_repair|decommissioned),
            attributes jsonb, last_service_at, next_service_due_at
asset_events id, tenant_id, asset_id, type (installed|serviced|repaired|
            moved|meter_reading|decommissioned), job_id, occurred_at,
            data jsonb
```

`asset_events` is the append-only service history. `assets.qr_code` is what an
engineer scans to jump straight to history and the correct form.

## 5. Field operations

```
job_types    id, tenant_id, name, code, color, default_duration_minutes,
             required_skill_ids uuid[], required_form_ids uuid[],
             checklist jsonb, default_priority
jobs         id, tenant_id, branch_id, number (per-tenant sequence),
             job_type_id, customer_id, site_id, asset_id, contract_id,
             title, description, priority (low|normal|high|emergency),
             status, source (manual|recurring|portal|api|integration),
             reported_by_contact_id, sla_policy_id,
             sla_respond_by, sla_resolve_by, sla_responded_at,
             sla_resolved_at, sla_breached boolean,
             scheduled_start, scheduled_end, actual_start, actual_end,
             parent_job_id (follow-up visits), recurrence_id,
             completion_notes, customer_signature_id,
             labour_cost, parts_cost, total_cost, version
tasks        id, tenant_id, job_id, sequence, title, description,
             estimated_minutes, status, required_form_id, completed_at
assignments  id, tenant_id, job_id, task_id, user_id, role_on_job
             (lead|helper), scheduled_start, scheduled_end,
             status (proposed|assigned|accepted|declined|reassigned),
             accepted_at, declined_reason
job_status_history
             id, tenant_id, job_id, from_status, to_status, changed_by,
             changed_at, reason, geo point, source (web|mobile|system)
time_entries id, tenant_id, job_id, user_id, type (travel|work|break),
             started_at, ended_at, duration_minutes, start_geo, end_geo,
             is_manual, approved_by
job_attachments id, tenant_id, job_id, file_id, kind (photo|document|
             signature), caption, taken_at, geo
recurrences  id, tenant_id, job_type_id, customer_id, site_id, asset_id,
             rrule text, timezone, generate_days_ahead, lead_time_days,
             last_generated_at, next_occurrence_at, is_active
sla_policies id, tenant_id, name, rules jsonb
             -- per priority: respond_within, resolve_within,
             -- business_hours_only, escalation_chain
```

Job status enum:
`draft → scheduled → dispatched → en_route → in_progress → on_hold →
completed → verified → closed`, plus `cancelled` and `failed_visit`.

`failed_visit` (customer absent, site inaccessible, part unavailable) is a real
outcome that must be recorded distinctly — it is a reported KPI and it triggers
rescheduling, not closure.

## 6. Dynamic forms

Detailed in `04-module-forms.md`; tables summarized here.

```
form_templates      id, tenant_id, key, name, description, category,
                    icon, status (draft|published|archived),
                    current_version_id, is_platform_template
form_versions       id, tenant_id, template_id, version_number,
                    schema jsonb, scoring_config jsonb, pdf_config jsonb,
                    published_at, published_by, change_note
                    -- IMMUTABLE once published
form_submissions    id, tenant_id, branch_id, form_version_id, job_id,
                    task_id, asset_id, site_id, customer_id,
                    submitted_by, status (draft|submitted|verified|rejected),
                    data jsonb, score numeric, max_score numeric,
                    passed boolean, started_at, submitted_at,
                    device_id, submitted_geo point,
                    verified_by, verified_at, rejection_reason,
                    pdf_file_id, client_uuid (idempotency), version
form_submission_values
                    id, tenant_id, submission_id, form_version_id,
                    field_key, field_type, repeat_index,
                    value_text, value_number, value_bool, value_date,
                    value_json
                    -- flattened projection for cross-submission reporting
form_field_library  id, tenant_id, key, label_i18n jsonb, field_type,
                    config jsonb   -- reusable fields across templates
```

`form_submission_values` is a derived projection, rebuilt from `data` by a
background job. It exists because querying "every inspection where pressure > 40
across six months" over raw JSONB does not scale.

## 7. Inventory

Detailed in `06-module-inventory.md`.

```
item_categories  id, tenant_id, name, parent_id
items            id, tenant_id, sku (unique per tenant), name, description,
                 category_id, unit_of_measure, barcode, is_serialized,
                 is_batch_tracked, has_expiry, is_stock_tracked,
                 default_cost, default_price, currency, reorder_point,
                 reorder_quantity, lead_time_days, manufacturer_part_number,
                 image_file_id, status
stock_locations  id, tenant_id, branch_id, name, code,
                 type (warehouse|van|site|supplier|customer|scrap|in_transit),
                 assigned_user_id, vehicle_id, parent_location_id, is_active
stock_movements  id, tenant_id, item_id, quantity numeric,
                 from_location_id, to_location_id,
                 type (receipt|transfer|consumption|return|adjustment|
                       count|scrap|reservation_release),
                 reference_type (job|purchase_order|count|manual),
                 reference_id, serial_id, batch_id, unit_cost, currency,
                 occurred_at, performed_by, note, reversal_of_id
                 -- APPEND ONLY. Never updated or deleted.
stock_levels     tenant_id, item_id, location_id, quantity_on_hand,
                 quantity_reserved, quantity_available, avg_cost,
                 last_movement_id, updated_at
                 -- derived rollup, rebuildable from movements
serials          id, tenant_id, item_id, serial_number, status
                 (in_stock|installed|faulty|returned|scrapped),
                 current_location_id, installed_asset_id, warranty_expires_at
batches          id, tenant_id, item_id, batch_number, expiry_date,
                 received_at, quantity_received
stock_counts     id, tenant_id, location_id, status, scheduled_for,
                 started_at, completed_at, counted_by, approved_by
stock_count_lines id, count_id, item_id, expected_qty, counted_qty,
                 variance, note
suppliers        id, tenant_id, name, contacts jsonb, payment_terms, status
purchase_orders  id, tenant_id, branch_id, supplier_id, number, status,
                 expected_at, total, currency, approved_by
purchase_order_lines id, po_id, item_id, quantity, unit_cost, received_qty
job_parts        id, tenant_id, job_id, item_id, quantity_planned,
                 quantity_used, serial_id, unit_cost, movement_id
```

`stock_levels` is a cache. If it ever disagrees with the sum of movements, the
movements win and the rollup is rebuilt. Corrections are made by posting a
reversing movement, never by editing history.

## 8. Meetings

```
meetings          id, tenant_id, branch_id, title, description, type
                  (internal|customer|toolbox_talk|training|review),
                  starts_at, ends_at, timezone, location, meeting_url,
                  customer_id, site_id, job_id, organizer_id,
                  recurrence_rrule, series_id, status
                  (scheduled|in_progress|completed|cancelled),
                  visibility (public|private)
meeting_attendees id, meeting_id, user_id, contact_id, external_email,
                  is_required, response (pending|accepted|declined|tentative),
                  responded_at, attended boolean, checked_in_at
meeting_agenda_items id, meeting_id, sequence, title, presenter_id,
                  duration_minutes, notes
meeting_minutes   id, meeting_id, content, recorded_by, approved_by,
                  approved_at
action_items      id, tenant_id, meeting_id, title, description,
                  assignee_id, due_date, priority, status,
                  converted_job_id, completed_at
user_availability id, tenant_id, user_id, weekday, start_time, end_time,
                  effective_from, effective_to
time_off          id, tenant_id, user_id, type (leave|sick|training|holiday),
                  starts_at, ends_at, status, approved_by
```

`user_availability` and `time_off` are shared by meetings **and** job dispatch —
a single source of truth for "is this person free", so an engineer cannot be
booked for a meeting and a job at the same time.

## 9. Notifications, files, audit, billing

```
files             id, tenant_id, storage_key, bucket, filename, mime_type,
                  size_bytes, checksum_sha256, width, height, duration_ms,
                  thumbnail_key, uploaded_by, entity_type, entity_id,
                  virus_scan_status, created_at
notification_templates id, tenant_id (null = system), key, channel,
                  locale, subject, body, provider_template_id
notification_preferences id, tenant_id, user_id, event_key, channels text[],
                  quiet_hours jsonb
notifications     id, tenant_id, user_id, event_key, title, body,
                  data jsonb, entity_type, entity_id, read_at, created_at
notification_deliveries id, notification_id, channel, provider,
                  provider_message_id, status (queued|sent|delivered|
                  failed|bounced), error, attempts, sent_at, delivered_at

audit_log         id, tenant_id, actor_user_id, impersonated_by,
                  action, entity_type, entity_id, before jsonb,
                  after jsonb, diff jsonb, ip, user_agent, request_id,
                  source (web|mobile|api|system), occurred_at
                  -- append-only; no update/delete grants for app role

subscriptions     id, tenant_id, plan_id, status, billing_cycle,
                  current_period_start, current_period_end,
                  seats_purchased, seats_used, provider,
                  provider_subscription_id, trial_ends_at, cancel_at
usage_records     id, tenant_id, metric (submissions|storage_gb|sms|whatsapp|
                  api_calls), quantity, period_start, period_end
invoices          id, tenant_id, number, status, subtotal, tax_amount,
                  tax_rate, total, currency, issued_at, due_at, paid_at,
                  provider_invoice_id, pdf_file_id, einvoice_status,
                  einvoice_uuid, einvoice_qr
payments          id, tenant_id, invoice_id, amount, currency, method,
                  provider, provider_payment_id, status, paid_at
```

## 10. Indexing notes

- Every lookup index leads with `tenant_id`.
- `jobs (tenant_id, branch_id, status, scheduled_start)` — the dispatch board's
  primary access path.
- `jobs (tenant_id, assigned_user_id, scheduled_start)` via a covering join for
  the mobile "my day" query.
- `stock_movements (tenant_id, item_id, location_id, occurred_at desc)`.
- `form_submissions (tenant_id, form_version_id, submitted_at desc)`.
- `form_submission_values (tenant_id, field_key, value_number)` and a matching
  text index for reporting filters.
- GIN on `form_submissions.data`, `assets.attributes`, `audit_log.diff`.
- Partial index `where deleted_at is null` on high-traffic soft-deleted tables.
- Postgres full-text index over customers, sites, assets and jobs for global
  search, with an Arabic text-search configuration.

## 11. Retention and growth

| Data | Growth driver | Policy |
|---|---|---|
| Photos | Largest by far: ~5–15 MB per job | Client-side compression, thumbnails, lifecycle to cold storage after 12 months |
| `form_submission_values` | ~20–60 rows per submission | Partition by month once past ~50M rows |
| `audit_log` | Every write | Partition monthly; retain 24 months hot, then archive |
| `job_status_history` | ~8 rows per job | Retain for the life of the job |
| `stock_movements` | Never purged — it is the ledger | Partition by year if needed |
| `notification_deliveries` | High volume, low value after send | Purge after 90 days |
