---
name: optimizing-database-indexes
description: Guidelines for creating efficient database indexes in Appwrite. Use to ensure search and filter operations stay fast. Use when this capability is needed.
metadata:
  author: itsmealee
---

# Database Indexing Strategy

## When to use this skill
- When queries are becoming slow.
- Before launching categories, search, or price sorting filters.

## Index Types
- **Key**: For simple equality checks (e.g., `userId`).
- **Fulltext**: For searching titles and descriptions.
- **Unique**: For fields like `email` or `slug` that must not repeat.

## Rules
- **Query Alignment**: Every field used in a `Query.equal` or `Query.orderAsc` should be indexed.
- **Index Order**: For compound queries, the order of attributes in the index matters (e.g., `Price` + `Status`).

## Instructions
- **Monitor**: Watch the Appwrite "Usage" tab to identify slow queries.
- **Limit**: Don't index every single field; it slows down writes (Creation/Updates).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itsmealee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
