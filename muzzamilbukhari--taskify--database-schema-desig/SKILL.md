---
name: database-schema-desig
description: name: database-schema-design Use when this capability is needed.
metadata:
  author: muzzamilbukhari
---
---
name: database-schema-design
description: Design relational database schemas, create tables, and manage migrations. Use for backend and full-stack applications.
---

# Database Schema & Migrations

## Instructions

1. **Schema design**
   - Identify entities and relationships
   - Normalize data (up to 3NF where applicable)
   - Define primary keys and foreign keys

2. **Table creation**
   - Choose appropriate data types
   - Apply constraints (NOT NULL, UNIQUE, CHECK)
   - Use indexes for frequently queried columns

3. **Migrations**
   - Version-controlled schema changes
   - Forward and rollback migrations
   - Separate schema and data migrations

## Best Practices
- Use snake_case for table and column names
- Prefer surrogate primary keys (e.g., id)
- Avoid over-normalization
- Always write reversible migrations
- Keep migrations small and atomic

## Example Structure
```sql
-- users table
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- posts table
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  user_id INT REFERENCES users(id) ON DELETE CASCADE,
  title VARCHAR(200) NOT NULL,
  content TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/muzzamilbukhari) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
