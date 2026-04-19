---
name: db-pipeline
description: Run database seed pipeline (schema, seed, aggregate) with status checks Use when this capability is needed.
metadata:
  author: vincentgagnondev
---

# Database Pipeline

Run the SafeNSound database seeding pipeline. Always confirm with the user before starting — this modifies the database.

## Default Pipeline (MVP2 data)

1. `npm run db:schema` — Create/update all tables via `scripts/create-schema.sql`
2. `npm run db:seed-mvp2` — Bulk-insert pre-geocoded offender records from `mvp2.csv`
3. `npm run db:aggregate-hexes` — Recompute `hex_aggregates` table for all hex resolutions

## Full Pipeline (all data sources)

If the user requests a full seed, run `npm run db:full` instead, which executes the complete pipeline including all 13 data tables.

## Verification

After seeding, verify success by querying row counts:

```sql
SELECT 'nsopw_offenders' as tbl, count(*) FROM nsopw_offenders
UNION ALL SELECT 'hex_aggregates', count(*) FROM hex_aggregates
UNION ALL SELECT 'public_schools', count(*) FROM public_schools
UNION ALL SELECT 'fast_food_locations', count(*) FROM fast_food_locations;
```

Use the Supabase MCP `execute_sql` tool for verification queries.

## Important Notes

- All `db:*` commands require `.env.local` with `POSTGRES_URL`
- `db:seed-mvp` (not mvp2) geocodes live via Nominatim and takes 3-5 minutes
- `db:aggregate-hexes` must run AFTER seeding to reflect new data in the hex grid

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vincentgagnondev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
