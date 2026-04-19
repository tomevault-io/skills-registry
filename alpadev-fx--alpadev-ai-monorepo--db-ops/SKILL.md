---
name: db-ops
description: Safe database management for PostgreSQL. Handles migrations and schema inspection. Use when this capability is needed.
metadata:
  author: alpadev-fx
---

# Database Operations Protocol

## Protocols
1. **Pre-Flight Check**: Before running ANY migration, ALWAYS list active queries to ensure no locks will occur.
2. **Tooling**: Use `golang-migrate` for all schema changes.
3. **Safety**: **NEVER** use `DROP TABLE` in production environments.

## Common Commands
- **Migration Status**: `migrate -path internal/db/migrations -database "$DATABASE_URL" version`
- **Apply Migration**: `migrate -path internal/db/migrations -database "$DATABASE_URL" up`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alpadev-fx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
