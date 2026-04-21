---
name: syntra-db
description: Database management with Syntra. Use when creating tables, defining schemas, querying records, inserting data, running SQL, or managing database structure. Covers CRUD operations, schema design, migrations, filtering, and PostgreSQL functions. Use when this capability is needed.
metadata:
  author: mauricioperera
---

# Syntra Database

## Schema design workflow

1. `database_list_tables` — see existing tables
2. `database_create_table` — create with typed columns
3. `database_get_table_schema` — verify the result
4. `database_alter_table` — modify later (add/drop/rename columns)

### Create table example

```json
{
  "table_name": "posts",
  "columns": [
    { "name": "id", "type": "uuid", "is_primary_key": true, "default_value": "gen_random_uuid()" },
    { "name": "title", "type": "text", "nullable": false },
    { "name": "body", "type": "text" },
    { "name": "author_id", "type": "uuid", "nullable": false },
    { "name": "published", "type": "boolean", "default_value": "false" },
    { "name": "created_at", "type": "timestamptz", "default_value": "now()" }
  ]
}
```

## CRUD operations

- **Insert**: `database_insert_records` with `records` array and optional `returning`
- **Select**: `database_select_records` with `filters`, `order`, `limit`, `offset`
- **Update**: `database_update_records` with `data` and `filters`
- **Delete**: `database_delete_records` with `filters`
- **Upsert**: `database_bulk_upsert` with `conflict_columns`

## Filters (PostgREST syntax)

Filters use `{"column": "operator.value"}` format:

| Operator | Meaning | Example |
|---|---|---|
| `eq` | Equals | `{"status": "eq.active"}` |
| `neq` | Not equals | `{"role": "neq.admin"}` |
| `gt` / `gte` | Greater than | `{"age": "gte.18"}` |
| `lt` / `lte` | Less than | `{"price": "lt.100"}` |
| `like` | Pattern match | `{"name": "like.%john%"}` |
| `ilike` | Case-insensitive pattern | `{"email": "ilike.%@gmail.com"}` |
| `is` | IS NULL / IS NOT NULL | `{"deleted_at": "is.null"}` |
| `in` | In list | `{"status": "in.(active,pending)"}` |

Order: `"created_at.desc"` or `"name.asc"`

## Raw SQL

- `database_execute_sql` — read-only (SELECT, WITH, EXPLAIN)
- `database_execute_sql_write` — any statement (INSERT, UPDATE, CREATE, ALTER, DROP)

Use parameterized queries with `params: ["$1_value", "$2_value"]`.

## Database metadata

- `database_list_functions` — PostgreSQL functions
- `database_list_indexes` — table indexes
- `database_list_policies` — Row Level Security policies
- `database_list_triggers` — table triggers
- `database_execute_rpc` — call a PostgreSQL function by name

## Column types and query patterns

- For PostgreSQL column types: see [column-types.md](references/column-types.md)
- For query pattern examples: see [query-patterns.md](references/query-patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mauricioperera) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
