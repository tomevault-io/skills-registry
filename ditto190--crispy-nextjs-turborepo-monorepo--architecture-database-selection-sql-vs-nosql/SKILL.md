---
name: architecture-database-selection-sql-vs-nosql
description: Imported TRAE skill from architecture/Database_Selection_SQL_vs_NoSQL.md Use when this capability is needed.
metadata:
  author: Ditto190
---

# Database Selection (SQL vs NoSQL)

## SQL (Relational)
- **Examples**: PostgreSQL, MySQL.
- **Use Case**: Structured data, complex relationships, transactions (ACID), reporting.
- **Schema**: Rigid, requires migrations.

## NoSQL (Non-Relational)
- **Examples**: MongoDB (Document), Redis (Key-Value), Cassandra (Wide-Column).
- **Use Case**: Unstructured data, high write throughput, horizontal scaling, flexible schema.
- **Schema**: Flexible/Schema-less.

## Selection
- Choose **SQL** by default for most business applications.
- Choose **NoSQL** for specific needs like caching (Redis), massive logs, or rapidly changing data structures.

---
> Source: [Ditto190/crispy-nextjs-turborepo-monorepo](https://github.com/Ditto190/crispy-nextjs-turborepo-monorepo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
