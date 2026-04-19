---
name: database-schema-design
description: Design robust database schemas with tables, relationships, and migrations. Use for scalable backend systems. Use when this capability is needed.
metadata:
  author: codewithsuleman
---

# Database Schema Design

## Instructions

1. **Schema Planning**
   - Identify entities and attributes
   - Define primary keys
   - Normalize data (avoid redundancy)
   - Choose appropriate data types

2. **Table Design**
   - Use meaningful table and column names
   - Apply constraints (NOT NULL, UNIQUE, CHECK)
   - Define indexes for frequently queried columns
   - Maintain consistency in naming conventions

3. **Relationships**
   - One-to-One (1:1)
   - One-to-Many (1:N)
   - Many-to-Many (M:N) using junction tables
   - Enforce referential integrity using foreign keys

4. **Migrations**
   - Version-controlled schema changes
   - Use up/down migrations
   - Never edit old migrations in production
   - Ensure migrations are reversible

5. **Scalability Considerations**
   - Avoid over-normalization
   - Plan for future growth
   - Use indexing wisely
   - Consider read/write patterns

## Best Practices
- Use snake_case for table and column names
- Always define primary keys
- Prefer UUIDs for distributed systems
- Add timestamps (`created_at`, `updated_at`)
- Keep migrations small and focused
- Document schema decisions

## Example Schema (SQL)

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codewithsuleman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
