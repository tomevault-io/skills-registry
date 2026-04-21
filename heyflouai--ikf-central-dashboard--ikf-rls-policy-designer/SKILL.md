---
name: ikf-rls-policy-designer
description: Designs Supabase schema + RLS policies for IKF CENTRAL privacy (auth-only access, org scoping, admin write controls) with a concrete test plan. Use when this capability is needed.
metadata:
  author: heyflouai
---

# IKF RLS Policy Designer

## Objective
Design a secure Supabase schema and RLS policy set that enforces total privacy.

## Constraints
- Only authenticated users can read anything.
- RLS enabled for all forecast tables.
- Users can only access rows for their org_id.
- Admins can ingest/modify; employees read-only.
- Avoid exposing raw forecast data unnecessarily; prefer views/endpoints returning only needed fields.

## Deliverables
- Proposed tables: profiles, organizations, user_roles, forecast_runs, forecasts (canonical rows).
- RLS policies per table.
- Recommended indexes (performance).
- A step-by-step RLS test plan (what to run as admin vs employee vs anon).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heyflouai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
