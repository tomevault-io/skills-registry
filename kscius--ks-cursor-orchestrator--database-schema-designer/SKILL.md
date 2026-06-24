---
name: database-schema-designer
description: Design robust, scalable database schemas for SQL and NoSQL databases. Provides normalization guidelines, indexing strategies, migration patterns, constraint design, and performance optimization. Ensures data integrity, query performance, and maintainable data models. Use when this capability is needed.
metadata:
  author: kscius
---

## When to use

- Design robust, scalable database schemas for SQL and NoSQL databases.

# Database Schema Designer

Design production-ready database schemas with best practices built-in.

---

## Quick Start

Just describe your data model:

```
design a schema for an e-commerce platform with users, products, orders
```

You'll get a complete SQL schema like:

```sql
CREATE TABLE users (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id),
  total DECIMAL(10,2) NOT NULL,
  INDEX idx_orders_user (user_id)
);
```

**What to include in your request:**
- Entities (users, products, orders)
- Key relationships (users have orders, orders have items)
- Scale hints (high-traffic, millions of records)
- Database preference (SQL/NoSQL) - defaults to SQL if not specified

---

## Triggers

| Trigger | Example |
|---------|---------|
| `design schema` | "design a schema for user authentication" |
| `database design` | "database design for multi-tenant SaaS" |
| `create tables` | "create tables for a blog system" |
| `schema for` | "schema for inventory management" |
| `model data` | "model data for real-time analytics" |
| `I need a database` | "I need a database for tracking orders" |
| `design NoSQL` | "design NoSQL schema for product catalog" |

---

> **On-demand loading:** Read this skill only when designing or evolving database schemas. For full normalization, migration, and optimization detail see [references/detailed-guide.md](references/detailed-guide.md).

---
> Source: [kscius/KS-Cursor-Orchestrator](https://github.com/kscius/KS-Cursor-Orchestrator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
