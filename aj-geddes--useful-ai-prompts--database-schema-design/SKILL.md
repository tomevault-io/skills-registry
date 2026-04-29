---
name: database-schema-design
description: > Use when this capability is needed.
metadata:
  author: aj-geddes
---

# Database Schema Design

## Table of Contents

- [Overview](#overview)
- [When to Use](#when-to-use)
- [Quick Start](#quick-start)
- [Reference Guides](#reference-guides)
- [Best Practices](#best-practices)

## Overview

Design scalable, normalized database schemas with proper relationships, constraints, and data types. Includes normalization techniques, relationship patterns, and constraint strategies.

## When to Use

- New database schema design
- Data model planning
- Table structure definition
- Relationship design (1:1, 1:N, N:N)
- Normalization analysis
- Constraint and trigger planning
- Performance optimization at schema level

## Quick Start

**PostgreSQL - Eliminate Repeating Groups:**

```sql
-- NOT 1NF: repeating group in single column
CREATE TABLE orders_bad (
  id UUID PRIMARY KEY,
  customer_name VARCHAR(255),
  product_ids VARCHAR(255)  -- "1,2,3" - repeating group
);

-- 1NF: separate table for repeating data
CREATE TABLE orders (
  id UUID PRIMARY KEY,
  customer_name VARCHAR(255),
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE order_items (
  id UUID PRIMARY KEY,
  order_id UUID NOT NULL,
  product_id UUID NOT NULL,
  quantity INTEGER NOT NULL,
  FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE
);
```

## Reference Guides

Detailed implementations in the `references/` directory:

| Guide | Contents |
|---|---|
| [First Normal Form (1NF)](references/first-normal-form-1nf.md) | First Normal Form (1NF) |
| [Second Normal Form (2NF)](references/second-normal-form-2nf.md) | Second Normal Form (2NF) |
| [Third Normal Form (3NF)](references/third-normal-form-3nf.md) | Third Normal Form (3NF) |
| [Entity-Relationship Patterns](references/entity-relationship-patterns.md) | Entity-Relationship Patterns |

## Best Practices

### ✅ DO

- Follow established patterns and conventions
- Write clean, maintainable code
- Add appropriate documentation
- Test thoroughly before deploying

### ❌ DON'T

- Skip testing or validation
- Ignore error handling
- Hard-code configuration values

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aj-geddes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
