---
name: database-selection-sql-vs-nosql
description: Guidance and best practices for database selection (sql vs nosql). Use when this capability is needed.
metadata:
  author: jyjeanne
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
> Source: [jyjeanne/ai-setup-forge](https://github.com/jyjeanne/ai-setup-forge) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
