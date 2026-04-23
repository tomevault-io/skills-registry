---
name: sql
description: SQL and database best practices including query optimization, indexing, and schema design. Use when this capability is needed.
metadata:
  author: kprsnt2
---

# SQL Best Practices

## Query Writing
- Use parameterized queries (prevent SQL injection)
- Select only needed columns (avoid SELECT *)
- Use appropriate JOINs
- Add WHERE clauses to limit results
- Use EXPLAIN to analyze queries

## Indexing
- Index columns used in WHERE/JOIN/ORDER BY
- Use composite indexes for multi-column queries
- Don't over-index (slows writes)
- Consider covering indexes
- Monitor index usage

## Schema Design
- Use appropriate data types
- Add constraints (NOT NULL, UNIQUE, FK)
- Normalize to 3NF, denormalize for performance
- Use UUIDs for distributed systems
- Add created_at/updated_at columns

## Transactions
- Keep transactions short
- Use appropriate isolation levels
- Handle deadlocks gracefully
- Use optimistic locking when appropriate

## Migrations
- Make migrations reversible
- Test migrations on copy of prod data
- Use small, focused migrations
- Never modify existing migrations
- Back up before migrating

## Performance
- Use connection pooling
- Implement pagination
- Cache frequent queries
- Use read replicas
- Monitor slow queries

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kprsnt2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
