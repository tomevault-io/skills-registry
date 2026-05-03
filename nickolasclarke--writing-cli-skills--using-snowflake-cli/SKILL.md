---
name: using-snowflake-cli
description: Use when exploring Snowflake schemas, running queries, or answering data questions via the snow CLI — covers schema discovery, query execution with safe defaults, output format selection, and avoiding context overflow from large results
metadata:
  author: nickolasclarke
---

# Using Snowflake CLI

## Overview

Execute SQL queries and explore schemas using Snowflake's `snow` CLI. Assumes CLI is pre-configured (auth handled). Focus on efficient exploration and safe result handling.

**Core principle:** Always limit results to avoid context overflow. Explore schema before writing queries.

## When to Use

- Exploring unfamiliar Snowflake schemas (databases, schemas, tables, columns)
- Running queries to answer data questions
- Validating query logic or dbt model output
- Investigating data quality or structure

## Workflow

### 1. Context Check

Before running anything, confirm what database, schema, and warehouse you're connected to. Without this, queries silently run against wrong defaults.

```bash
snow sql -q "SELECT CURRENT_DATABASE(), CURRENT_SCHEMA(), CURRENT_WAREHOUSE()" --format JSON
```

If the result shows NULLs, set context explicitly:

```bash
snow sql -q "USE DATABASE my_db; USE SCHEMA my_schema; USE WAREHOUSE my_wh"
```

Or pass overrides on every command: `--database X --schema Y`

### 2. Schema Discovery

Start broad, narrow down. Never write a query against a table you haven't inspected.

1. **List databases/schemas** — orient yourself
2. **Find tables by name** — search with LIKE patterns
3. **Describe table** — check column names and types before querying
4. **Sample data** — SELECT with LIMIT to see actual values

Use `snow sql` with SHOW/DESCRIBE SQL rather than `snow object` commands. The `snow object list` TABLE output is often unreadable for wide columns.

```bash
# List schemas in a database
snow sql -q "SHOW SCHEMAS IN DATABASE ANALYTICS" --format JSON

# Find tables matching a pattern
snow sql -q "SHOW TABLES LIKE '%CUSTOMER%' IN SCHEMA ANALYTICS.PUBLIC" --format JSON

# Inspect columns before querying
snow sql -q "DESCRIBE TABLE ANALYTICS.PUBLIC.CUSTOMERS" --format JSON

# Sample actual data
snow sql -q "SELECT * FROM ANALYTICS.PUBLIC.CUSTOMERS LIMIT 10" --format JSON
```

### 3. Query Execution

**Defaults for every query:**
- `LIMIT 100` unless you have a specific reason for more
- `--format JSON` for reliable output
- `--silent` to suppress progress messages when output matters

**Before running a query with uncertain result size:**

```bash
snow sql -q "SELECT COUNT(*) FROM large_table WHERE condition" --format JSON
```

**Execution modes:**

| Mode | Command | Best For |
|------|---------|----------|
| Single query | `snow sql -q "SELECT..."` | Exploration, one-off questions |
| SQL file | `snow sql -f query.sql` | Multi-statement scripts |
| With variables | `snow sql -q "SELECT * FROM &{tbl}" -D tbl=customers` | Parameterized queries |

**For large results:**
- Export to file: `snow sql -q "SELECT ..." --format CSV > output.csv`
- Aggregate instead of dumping raw rows
- Break into smaller queries with pagination (`LIMIT N OFFSET M`)

### 4. Output Handling

| Format | When to Use |
|--------|-------------|
| JSON | Default. Reliable for any data shape, parseable, handles wide columns |
| TABLE | Only for narrow results (3-4 short columns). Wraps and mangles on wide data |
| CSV | Export to file for large results or downstream processing |

**TABLE format will produce garbled output** when tables have many columns or long string values. When in doubt, use JSON.

## CLI Reference

| Tool | Reference |
|------|-----------|
| `snow` CLI | See [references/snowflake-cli.md](references/snowflake-cli.md) |

## Common Mistakes

**Unbounded query:** `SELECT *` without LIMIT returns thousands of rows, flooding the context window and making the agent unable to continue.
**Fix:** Always include `LIMIT 100`. Count rows first if result size is uncertain.

**TABLE format on wide data:** Columns wrap across lines, producing unreadable output that wastes tokens.
**Fix:** Use `--format JSON` by default. Reserve TABLE for narrow results you want to display to the user.

**Querying before exploring:** Writing complex joins against the wrong table or wrong column names.
**Fix:** DESCRIBE the table and sample data with LIMIT 10 before writing any non-trivial query.

**Not checking context:** Running queries without knowing what database/schema is active, then getting "object does not exist" errors or querying the wrong data.
**Fix:** Always start with the context check query. Use fully-qualified names (`DB.SCHEMA.TABLE`) when working across schemas.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickolasclarke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
