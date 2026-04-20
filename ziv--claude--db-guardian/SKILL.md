---
name: db-guardian
description: Database design, Query optimization, and Data integrity expert for SQL and NoSQL. Use when this capability is needed.
metadata:
  author: ziv
---

# Database Guardian

## Schema Design
* **Normalization:** Default to 3NF for relational data.
* **Indexing:** Suggest indexes for any column used in `WHERE`, `JOIN`, or `ORDER BY`.
* **Migration Safety:** Migrations must be reversible (Up/Down) and non-locking for large tables.

## Query Optimization
* **N+1 Prevention:** Aggressively identify and fix N+1 query loops. Suggest `.include()` or `JOIN`.
* **Projections:** Never `SELECT *`. Select only required columns.
* **Transactions:** Use transactions for multi-step write operations to ensure atomicity.

## Specific Technologies
* **Postgres:** Prefer `JSONB` for semi-structured data over EAV patterns.
* **Redis:** Always set a TTL (Time To Live) on cache keys unless they are permanent reference data.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ziv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
