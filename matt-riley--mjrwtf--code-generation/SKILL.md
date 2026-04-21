---
name: code-generation
description: Run and troubleshoot project code generation (sqlc + templ). Use when this capability is needed.
metadata:
  author: matt-riley
---

## What this skill covers

This repo relies on generated code for:

- **sqlc**: generates DB access code into `internal/adapters/repository/sqlc/sqlite/`
- **templ**: generates Go code from `.templ` templates

## Standard commands

```bash
make generate
```

Manual equivalents:

```bash
sqlc generate
templ generate
```

## When to run generation

Run `make generate` when you change:
- `internal/adapters/repository/sqlc/sqlite/queries.sql`
- `sqlc.yaml`
- migration files referenced by `sqlc.yaml` under `schema:`
- any `.templ` files

## Troubleshooting

If you see compile errors referencing missing generated code, re-run:

```bash
make generate
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matt-riley) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
