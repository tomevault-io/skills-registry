---
name: supabase-backend
description: Expert in Supabase architecture, SQL optimization (PostgreSQL), and backend security (RLS) for real-time tracking systems. Use when this capability is needed.
metadata:
  author: abelv22
---

# Supabase & Backend Architecture Skill

This skill enables the assistant to provide high-level architectural advice and implementation details for the iTaxiBcn backend.

## Knowledge Areas

### 1. Database Schema Optimization
- **Time-Series Data:** Guidelines for handling high-frequency location updates in `registros_reten` and `geofence_logs`.
- **Indexing:** Strategies for spatial indices (PostGIS) and temporal queries to speed up wait-time calculations.
- **Materialized Views:** Recommendation for replacing heavy queries on `registros_reten` with materialized views for zone aggregations.

### 2. Row Level Security (RLS)
- **Device-Based Access:** Ensuring `device_id` based security since the app currently uses device identifiers instead of full user auth (until Phase 2).
- **Audit Logs:** Best practices for `geofence_logs` and `location_debug_logs` security.

### 3. Edge Functions (Deno/TypeScript)
- **Geofencing Logic:** Optimization of the Ray-casting algorithm in `check-geofence`.
- **Performance:** Minimizing startup time and memory footprint of edge functions.
- **Error Handling:** Robust try-catch patterns and standard JSON responses.

### 4. SQL Scripting
- **Migrations:** Following the `supabase/migrations/` structure.
- **Stored Procedures:** Writing efficient PL/pgSQL for complex logic like `useWhereNext` score calculation on the server side.

## Guidelines for Responses
- Always suggest **Materialized Views** for dashboard metrics that don't need second-by-second accuracy.
- When writing SQL, ensure **idempotency** (use `CREATE OR REPLACE` or `IF NOT EXISTS`).
- Prioritize **PostGIS** functions for distance and polygon math.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abelv22) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
