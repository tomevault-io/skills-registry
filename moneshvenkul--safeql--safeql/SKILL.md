---
name: safeql
description: Read-only SQL database inspection with the safeql CLI. Use when a user wants to inspect PostgreSQL, MySQL, or SQLite data safely without granting write access. Triggers include database debugging, checking rows, validating state, looking up sessions, inspecting schemas, reviewing production data carefully, or any task where an agent should query a database but must not mutate it. Use when this capability is needed.
metadata:
  author: moneshvenkul
---

# safeql

Use `safeql` for guarded read-only database access.

Prefer it over raw database shells when the goal is inspection, debugging, or validation rather than writes.

## Quick Start

Prefer an existing profile first:

```bash
safeql profile list
```

If the `default` profile already exists, use it directly:

```bash
safeql query --sql "SELECT 1"
```

If no profile exists, prefer an env-backed profile:

```bash
safeql profile save --driver postgres --dsn-env DATABASE_URL --env-file .env
safeql query --sql "SELECT 1"
```

## Workflow

1. Check whether a saved profile already exists:

```bash
safeql profile list
```

2. If needed, create a connection profile.

Preferred:

```bash
safeql profile save --driver postgres --dsn-env DATABASE_URL --env-file .env
```

Convenience fallback:

```bash
safeql profile save --driver postgres --dsn "$DATABASE_URL"
```

3. Confirm connectivity with a tiny query:

```bash
safeql query --sql "SELECT 1"
```

4. Run small targeted inspection queries with `LIMIT` unless the user clearly needs a full result set:

```bash
safeql query --sql "SELECT * FROM users LIMIT 10"
```

5. Switch to JSON when the result needs to be consumed programmatically:

```bash
safeql query --format json --sql "SELECT id, email FROM users LIMIT 10"
```

6. If you need more examples, read `references/usage.md`.

## Rules

- Prefer env-backed profiles over stored DSNs.
- Use the implicit `default` profile when it exists.
- Use `LIMIT` for exploratory queries.
- Prefer narrow column selection over `SELECT *` when the relevant fields are known.
- Use `--format json` when downstream parsing matters.
- Do not try to use `safeql` for writes, migrations, or admin mutation.

## Query Shapes

`safeql` is intentionally conservative.

Allowed starting statements:

- `SELECT`
- `WITH`
- `SHOW`
- `EXPLAIN`

Rejected mutation or session-changing patterns include:

- `INSERT`
- `UPDATE`
- `DELETE`
- `ALTER`
- `DROP`
- `CREATE`
- `TRUNCATE`
- `GRANT`
- `REVOKE`
- `SET`

It also rejects multiple SQL statements in one invocation.

## Connection Guidance

Profiles support two modes:

- `env`
  - preferred
  - stores only driver plus env lookup metadata
- `encrypted_dsn`
  - convenience mode
  - encrypted at rest, but not strong secret management

If safety matters, keep the DSN outside `safeql` and use env-backed profiles.

## Driver Notes

- PostgreSQL, MySQL, and SQLite are supported.
- SQLite connections are forced to read-only mode; write-capable SQLite DSNs are rejected.
- If the driver is obvious from the DSN, `--driver auto` can infer it.

## Reference Files

Load on demand:

- `references/usage.md`
  - common commands
  - connection examples
  - schema inspection examples
  - JSON output patterns

---
> Source: [moneshvenkul/safeql](https://github.com/moneshvenkul/safeql) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
