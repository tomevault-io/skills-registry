---
name: database-schema-design
description: Best practices for designing SQL schemas. Use when creating tables, relationships, or indexes. Use when this capability is needed.
metadata:
  author: wynautbhav
---

# Database Schema Design Skill

## Rules
1. **Naming**: snake_case for columns (`user_id`, `created_at`). Plural for table names (`users`, `orders`).
2. **Primary Keys**: Always use a Primary Key (ID or UUID).
3. **Foreign Keys**: Index all foreign keys to speed up joins.
4. **Timestamps**: Every table should have `created_at` and `updated_at`.
5. **Normalization**: Avoid duplicating data. If data is repeated, move it to a new table.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wynautbhav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
