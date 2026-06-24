---
name: database
description: Guidelines for schema design, migrations, queries, data modeling, indexing, and data integrity Use when this capability is needed.
metadata:
  author: xmenq
---

# Database Skill

## When to use this skill

Use when designing schemas, writing migrations, creating queries, modeling data relationships, or working on data access layers.

---

## Data Modeling Principles

### 1. Start with the domain, not the database
- Model your domain entities first (in `types/`)
- Then map them to database tables/collections
- The domain model drives the schema, not the other way around

### 2. Normalize, then denormalize intentionally
- Start with normalized schema (3NF)
- Denormalize only when you have measured performance evidence
- Document every denormalization with the reason why

### 3. Schema is a contract
- Schema changes are **breaking changes** — treat them with the same care
- Every schema change needs a migration
- Every migration needs a rollback plan

---

## Schema Design Rules

### Naming conventions
| Element | Convention | Example |
|---------|-----------|---------|
| Tables | plural snake_case | `user_profiles`, `order_items` |
| Columns | singular snake_case | `first_name`, `created_at` |
| Primary keys | `id` | `id` (auto-generated) |
| Foreign keys | `<referenced_table_singular>_id` | `user_id`, `order_id` |
| Boolean columns | `is_`/`has_` prefix | `is_active`, `has_verified` |
| Timestamps | `_at` suffix | `created_at`, `updated_at`, `deleted_at` |
| Indexes | `idx_<table>_<columns>` | `idx_users_email` |

### Required columns on every table
```sql
id          -- Primary key (UUID or auto-increment)
created_at  -- When the record was created (UTC timestamp)
updated_at  -- When the record was last modified (UTC timestamp)
```

### Soft delete pattern
Prefer soft deletes over hard deletes for important data:
```sql
deleted_at  -- NULL if active, timestamp if soft-deleted
```
Always filter by `WHERE deleted_at IS NULL` in queries (or use a view/scope).

---

## Migrations

### Rules
- **One change per migration** — don't bundle unrelated schema changes
- **Always write both `up` and `down`** — every migration must be reversible
- **Never modify a deployed migration** — create a new migration instead
- **Test migrations against real-sized data** — tiny test DBs hide performance problems
- **Name migrations descriptively** — `add_email_index_to_users` not `migration_042`

### Migration checklist
- [ ] Has both `up` and `down` (forward and rollback)
- [ ] Tested with existing data (not just empty tables)
- [ ] Doesn't lock tables for extended periods on large datasets
- [ ] Backward compatible (old code can still work during deploy)
- [ ] Documented in the exec plan decision log

### Zero-downtime migration pattern
For breaking schema changes, use the expand-contract pattern:
1. **Expand** — add new column/table alongside old one
2. **Migrate** — backfill data, update code to write to both
3. **Switch** — update code to read from new location
4. **Contract** — remove old column/table after verification

---

## Query Patterns

### Rules
- **Always parameterize queries** — never string-concatenate user input
- **Select only needed columns** — no `SELECT *` in application code
- **Paginate large result sets** — never return unbounded rows
- **Use transactions for multi-step operations** — ensure atomicity
- **Index columns used in WHERE, JOIN, ORDER BY** — but don't over-index

### Query performance checklist
- [ ] Uses indexes effectively (check EXPLAIN plan)
- [ ] No N+1 query patterns (use JOINs or batch loading)
- [ ] Results are paginated (limit + offset or cursor-based)
- [ ] Large operations use batch processing
- [ ] Timeouts configured for long-running queries

### Pagination
Prefer **cursor-based pagination** for large/changing datasets:
```
-- Instead of: OFFSET 1000 LIMIT 20 (slow for high offsets)
-- Use: WHERE id > :last_seen_id ORDER BY id LIMIT 20
```

Use offset pagination only for small, static datasets.

---

## Indexing Strategy

### When to add an index
| Scenario | Index type |
|----------|-----------|
| Filter by column (`WHERE x = ?`) | Single column index |
| Filter by multiple columns | Composite index (most selective column first) |
| Sort results (`ORDER BY x`) | Index on sort column |
| Unique constraint | Unique index |
| Full-text search | Full-text / GIN index |
| JSON field queries | GIN / expression index |

### When NOT to add an index
- Tables with < 1000 rows (full scan is faster)
- Columns with very low cardinality (e.g., boolean flags)
- Write-heavy tables where index maintenance outweighs read benefits
- Columns rarely used in WHERE/JOIN/ORDER BY

---

## Data Integrity

### Constraints (use the database, not just the application)
- **NOT NULL** — unless the column is genuinely optional
- **UNIQUE** — for columns that must be unique (email, username, slug)
- **FOREIGN KEY** — for all references between tables
- **CHECK** — for value constraints (e.g., `quantity > 0`, `status IN (...)`)
- **DEFAULT** — for columns with sensible defaults

### Validation layers
1. **UI** — immediate feedback (client-side)
2. **API** — schema validation at the boundary
3. **Database** — constraints as the last line of defense

> All three layers should agree, but the database is the ultimate truth.

---

## Backup & Recovery

- Define backup frequency per data criticality
- Test restore procedures regularly
- Document recovery time objectives (RTO) and recovery point objectives (RPO)
- Store backups in a different region/zone than primary data

---

## PR Checklist for Database Changes

- [ ] Migration has both up and down
- [ ] Migration tested with existing data
- [ ] No `SELECT *` in new queries
- [ ] Queries are parameterized (no SQL injection risk)
- [ ] Appropriate indexes added for new queries
- [ ] Constraints added (NOT NULL, UNIQUE, FK, CHECK)
- [ ] Backward compatible during deployment
- [ ] Performance tested with realistic data volume
- [ ] Rollback plan documented in exec plan

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xmenq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
