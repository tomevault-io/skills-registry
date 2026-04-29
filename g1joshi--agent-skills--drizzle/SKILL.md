---
name: drizzle
description: Drizzle TypeScript ORM with SQL-like syntax. Use for database access. Use when this capability is needed.
metadata:
  author: g1joshi
---

# Drizzle ORM

Drizzle is the lightweight challenger to Prisma. v0.30+ (2025) focuses on **SQL-like** syntax, zero dependencies at runtime, and extreme cold-start performance.

## When to Use

- **Serverless**: Zero runtime overhead makes it perfect for Lambdas.
- **SQL Lovers**: You prefer `db.select().from(users)` over object APIs.
- **Performance**: Faster query building than Prisma.

## Core Concepts

### Schema Definition

Define tables in TS. `export const users = pgTable('users', { ... })`.

### Query Builder

Composable, type-safe SQL builder.

### Relational API

`db.query.users.findMany({ with: { posts: true } })` (Prisma-like convenience).

## Best Practices (2025)

**Do**:

- **Use `drizzle-kit`**: For migration management.
- **Use `run`**: Explicit execution model.
- **Use Driver Specifics**: Drizzle embraces database-specific features (JSONB, Arrays).

**Don't**:

- **Don't over-abstract**: Drizzle shines when you stay close to SQL.

## References

- [Drizzle ORM Documentation](https://orm.drizzle.team/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
