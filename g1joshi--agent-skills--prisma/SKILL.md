---
name: prisma
description: Prisma TypeScript ORM with migrations. Use for database access. Use when this capability is needed.
metadata:
  author: g1joshi
---

# Prisma

Prisma 6 (2025) adds multi-schema support and **Edge support** (Cloudflare Workers) via driver adapters. It is known for its type-safe client.

## When to Use

- **TypeScript**: The `PrismaClient` generated types are unmatched.
- **Relational DBs**: Postgres, MySQL, SQL Server.
- **Productivity**: `schema.prisma` is an easy-to-read source of truth.

## Core Concepts

### Schema (`schema.prisma`)

Defines models and relationships.

### Prisma Client

Auto-generated query builder. `prisma.user.findMany()`.

### Migrate

Declarative migrations. `prisma migrate dev`.

## Best Practices (2025)

**Do**:

- **Use Driver Adapters**: For serverless/edge environments (`@prisma/adapter-neon`).
- **Use `relationMode = "prisma"`**: If utilizing PlanetScale or Vitess.
- **Check generated SQL**: Use `$on('query')` to verify performance.

**Don't**:

- **Don't N+1**: Be careful with nested reads inside loops. Use `include`.

## References

- [Prisma Documentation](https://www.prisma.io/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
