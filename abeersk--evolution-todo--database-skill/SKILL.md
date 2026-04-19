---
name: database-skill
description: Design and implement database schemas, create tables, and manage migrations. Use for backend development and data modeling. Use when this capability is needed.
metadata:
  author: abeersk
---

# Database Skill – Tables, Migrations, Schema Design

## Instructions

1. **Schema Design**
   - Identify entities and relationships
   - Define primary keys and foreign keys
   - Normalize tables to reduce redundancy

2. **Table Creation**
   - Use SQL `CREATE TABLE` statements
   - Specify appropriate data types
   - Include constraints (NOT NULL, UNIQUE, DEFAULT)

3. **Migrations**
   - Use migration tools (e.g., Prisma, TypeORM, Django migrations)
   - Version control database schema changes
   - Test migrations before applying to production

## Best Practices
- Use descriptive table and column names
- Index columns frequently used in queries
- Keep tables normalized but denormalize when necessary for performance
- Maintain migration scripts in version control
- Avoid direct edits on production database

## Example Structure
```sql
-- Users table
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(150) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Posts table
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  user_id INT REFERENCES users(id),
  title VARCHAR(200) NOT NULL,
  content TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abeersk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
