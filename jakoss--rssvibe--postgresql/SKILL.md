---
name: postgresql
description: Performance and design guidelines for PostgreSQL database in RSSVibe, including connection pooling, JSONB columns, and query optimization. Use this skill when optimizing database performance. Use when this capability is needed.
metadata:
  author: jakoss
---

# PostgreSQL Database

## Performance & Design

- MUST use connection pooling for efficient connection management
- MUST use JSONB columns for semi-structured data (avoid creating many tables for flexible schemas)
- MUST create indexes on frequently queried columns to improve read performance

---

## Query Optimization

- SHOULD monitor query plans for expensive operations
- SHOULD use partial indexes when filtering on specific conditions
- SHOULD consider materialized views for complex aggregations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jakoss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
