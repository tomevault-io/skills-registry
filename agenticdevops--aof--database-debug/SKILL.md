---
name: database-debug
description: Database debugging and query execution Use when this capability is needed.
metadata:
  author: agenticdevops
---

# Database Debug Skill

Debug database issues and execute queries for diagnostics.

## When to Use This Skill

- Checking database connectivity
- Executing diagnostic queries
- Investigating slow queries
- Checking table structure
- Analyzing data

## Steps

1. **Connect to database** — `psql -h host -U user dbname`
2. **Check schema** — `\dt` or `SHOW TABLES;`
3. **Analyze query** — `EXPLAIN ANALYZE query;`
4. **Check locks** — `SELECT * FROM pg_locks;`
5. **Check size** — `SELECT pg_size_pretty(pg_total_relation_size(...));`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agenticdevops) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
