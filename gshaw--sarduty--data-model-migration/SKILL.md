---
name: data-model-migration
description: Add or change database-backed models using Ecto + SQLite conventions in this codebase. Use when this capability is needed.
metadata:
  author: gshaw
---

## Scope

Use this skill when introducing or modifying persisted data.

## Checklist

- Generate migrations via `mix ecto.gen.migration` and update `priv/repo/migrations/*`.
- Define schemas in `lib/app/model` with `use App, :model`, explicit `field` types, and `timestamps`.
- Validate with `App.Validate` helpers and `App.Field` types (e.g. `Field.TrimmedString`).
- Avoid casting programmatically-set fields; set them explicitly on the struct when creating records.
- Use `Ecto.Changeset.get_field/2` to read changeset values (no access syntax).
- Preload associations before rendering in templates.
- Add or update fixtures in `test/support/fixtures` when tests need new data.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gshaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
