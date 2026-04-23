---
name: mongodb
description: MongoDB best practices including schema design, indexing, and query optimization. Use when this capability is needed.
metadata:
  author: kprsnt2
---

# MongoDB Best Practices

## Schema Design
- Embed data for 1:few relationships
- Reference data for 1:many relationships
- Avoid unbounded arrays
- Use meaningful field names
- Plan for your query patterns

## Indexing
- Index fields used in queries
- Use compound indexes wisely
- Monitor index size
- Use TTL indexes for expiring data
- Text indexes for search

## Queries
- Use projection to limit fields
- Use aggregation pipeline for complex queries
- Avoid $where and $regex when possible
- Use explain() to analyze queries
- Paginate with skip/limit or cursor

## Performance
- Use connection pooling
- Shard for horizontal scaling
- Use read preferences
- Enable compression
- Monitor with mongotop/mongostat

## Security
- Enable authentication
- Use role-based access control
- Encrypt data at rest
- Use TLS for connections
- Validate input data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kprsnt2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
