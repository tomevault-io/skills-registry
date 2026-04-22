---
name: ecto-thinking
description: Apply when adding database tables, creating contexts, querying DB, adding schema fields, validating form input, fixing N+1 queries, preloading associations, or when the user mentions Repo, changesets, migrations, Ecto.Multi, has_many, belongs_to, transactions, query composition, or how contexts should talk. Use when this capability is needed.
metadata:
  author: gissandrogama
---

# Ecto Thinking

**Resumo (pt-BR):** Contexto como bounded context (DDD). Entre contextos use IDs, não associations. Múltiplos changesets por schema; preload vs join; gotchas com CTE, pgbouncer, sandbox e null bytes.

Mental shifts for Ecto and data layer design. These insights challenge typical ORM patterns.

## Context = Setting That Changes Meaning

Context isn't just a namespace—it changes what words mean. Think top-down: Subdomain → Context → Entity.

## Cross-Context References: IDs, Not Associations

Use `field :product_id, :integer` in another context; query through the context, not across associations. Keeps contexts independent and testable.

## DDD Patterns as Pipelines

Build → validate → insert. Use events (as data structs) to compose bounded contexts with minimal coupling.

## Schema ≠ Database Table

- Database table: standard `schema/2`
- Form validation only: `embedded_schema/1`
- API request/response: embedded schema or schemaless

## Multiple Changesets per Schema

Different operations = different changesets (e.g. registration_changeset, profile_changeset, admin_changeset).

## Multi-Tenancy

Composite foreign keys; use `prepare_query/3` for automatic scoping. CTEs don't inherit schema prefix—set explicitly: `%{recursive_query | prefix: "tenant"}`.

## Preload vs Join

- Separate preloads: has-many with many records (less memory)
- Join preloads: belongs-to, has-one (single query). Join preloads can use 10x more memory for has-many.

## Gotchas

- **Parameterized ≠ prepared:** pgbouncer → use `prepare: :unnamed`
- **pool_count vs pool_size:** single larger pool often better for mixed workloads
- **Sandbox:** Doesn't work with external processes (Cachex, GenServers)—they don't share the transaction
- **Null bytes:** PostgreSQL rejects them; sanitize with `String.replace(string, "\x00", "")`
- **preload_order:** Works for associations; not for `through`
- **Runtime migrations:** Use list API with `Ecto.Migrator.run/4`

## Idioms

- Prefer `Repo.insert/1` over `Repo.insert!/1`
- Use `Repo.transact/1` (Ecto 3.12+) for simple transactions instead of Ecto.Multi when appropriate

## Red Flags - STOP and Reconsider

- belongs_to pointing to another context's schema
- Single changeset for all operations
- Preloading has-many with join
- CTEs in multi-tenant apps without explicit prefix
- Using pgbouncer without `prepare: :unnamed`
- Accepting user input without null byte sanitization

**Any of these? Re-read the Gotchas section.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
