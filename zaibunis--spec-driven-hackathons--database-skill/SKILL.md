---
name: database-schema-design
description: Design relational database schemas, create tables, and manage migrations for scalable applications. Use when this capability is needed.
metadata:
  author: zaibunis
---

# Database Schema Design

## Instructions

1. **Schema planning**
   - Identify core entities
   - Define relationships (1–1, 1–many, many–many)
   - Normalize data where appropriate

2. **Table creation**
   - Use clear, consistent naming conventions
   - Define primary and foreign keys
   - Apply proper data types and constraints

3. **Migrations**
   - Create reversible migrations
   - Version-control schema changes
   - Avoid breaking changes in production

## Best Practices
- Use snake_case or camelCase consistently
- Index frequently queried columns
- Enforce data integrity with constraints
- Design with scalability in mind
- Document schema decisions

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
  user_id INTEGER REFERENCES users(id),
  title VARCHAR(255) NOT NULL,
  content TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zaibunis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
