---
name: design-database-schema
description: Design database schemas with proper normalization, relationships, and constraints Use when this capability is needed.
metadata:
  author: luanps2
---

# Design Database Schema Skill

## Purpose

This skill guides you through designing efficient, normalized database schemas with proper relationships, constraints, and indexes.

## When to Use

- Creating new database tables
- Designing data models for new features
- Refactoring existing schemas
- Need to ensure data integrity and performance

## Prerequisites

- Understanding of data requirements
- Knowledge of relationships between entities
- Access patterns (how data will be queried)
- Performance requirements

## Step-by-Step Process

### 1. Identify Entities and Attributes

**Example: E-commerce System**

Entities:
- User (id, email, password_hash, name, created_at)
- Product (id, name, description, price, stock, created_at)
- Order (id, user_id, total, status, created_at)
- OrderItem (id, order_id, product_id, quantity, price)

**Checklist:**
- [ ] List all entities (nouns in requirements)
- [ ] List attributes for each entity
- [ ] Identify candidate keys
- [ ] Determine data types

### 2. Define Relationships

```
User ──< Order (One user has many orders)
Order ──< OrderItem (One order has many items)
Product ──< OrderItem (One product appears in many order items)
```

**Relationship Types:**
- **One-to-One (1:1)**: User ──── Profile
- **One-to-Many (1:N)**: User ──< Post
- **Many-to-Many (M:N)**: Student ><──< Course (requires junction table)

**Checklist:**
- [ ] Identify all relationships
- [ ] Determine relationship cardinality
- [ ] Create junction tables for M:N relationships
- [ ] Define foreign keys

### 3. Apply Normalization

**First Normal Form (1NF)**
- Atomic values (no arrays or nested objects in SQL)
- Each row is unique
- No repeating groups

❌ **Violates 1NF:**
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  product_ids TEXT -- '1,2,3,4' (comma-separated)
);
```

✅ **Follows 1NF:**
```sql
CREATE TABLE order_items (
  id INT PRIMARY KEY,
  order_id INT,
  product_id INT
);
```

**Second Normal Form (2NF)**
- Must be in 1NF
- No partial dependencies (all non-key attributes depend on entire primary key)

**Third Normal Form (3NF)**
- Must be in 2NF
- No transitive dependencies (non-key attributes don't depend on other non-key attributes)

❌ **Violates 3NF:**
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  user_email VARCHAR(255), -- Depends on user_id, not order id
  total DECIMAL
);
```

✅ **Follows 3NF:**
```sql
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT REFERENCES users(id),
  total DECIMAL
);

CREATE TABLE users (
  id INT PRIMARY KEY,
  email VARCHAR(255)
);
```

**Checklist:**
- [ ] Ensure 1NF (atomic values)
- [ ] Ensure 2NF (no partial dependencies)
- [ ] Ensure 3NF (no transitive dependencies)
- [ ] Consider denormalization for read-heavy tables (carefully!)

### 4. Create ER Diagram

```
┌─────────────┐         ┌──────────────┐
│    users    │         │   products   │
├─────────────┤         ├──────────────┤
│ id (PK)     │         │ id (PK)      │
│ email       │         │ name         │
│ password    │         │ price        │
│ name        │         │ stock        │
│ created_at  │         │ created_at   │
└──────┬──────┘         └──────┬───────┘
       │                       │
       │ 1                     │ 1
       │                       │
       │ N                     │ N
       │                       │
┌──────┴──────┐         ┌──────┴────────┐
│   orders    │    N:M  │  order_items  │
├─────────────┤←────────┤───────────────┤
│ id (PK)     │         │ id (PK)       │
│ user_id(FK) │         │ order_id (FK) │
│ total       │         │ product_id(FK)│
│ status      │         │ quantity      │
│ created_at  │         │ price         │
└─────────────┘         └───────────────┘
```

### 5. Write Table Definitions

```sql
-- PostgreSQL Example

-- Users table
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(100) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),
  deleted_at TIMESTAMP -- Soft delete
);

-- Products table
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  description TEXT,
  price DECIMAL(10, 2) NOT NULL CHECK (price >= 0),
  stock INT NOT NULL DEFAULT 0 CHECK (stock >= 0),
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW()
);

-- Orders table
CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  total DECIMAL(10, 2) NOT NULL CHECK (total >= 0),
  status VARCHAR(50) NOT NULL DEFAULT 'pending',
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),
  
  CONSTRAINT valid_status CHECK (status IN ('pending', 'processing', 'shipped', 'delivered', 'cancelled'))
);

-- Order Items table (junction table)
CREATE TABLE order_items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  order_id UUID NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE RESTRICT,
  quantity INT NOT NULL CHECK (quantity > 0),
  price DECIMAL(10, 2) NOT NULL CHECK (price >= 0), -- Price at time of order
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  
  UNIQUE(order_id, product_id) -- Prevent duplicate items in same order
);
```

**Table Definition Checklist:**
- [ ] Primary keys defined
- [ ] Foreign keys with proper references
- [ ] NOT NULL constraints where appropriate
- [ ] UNIQUE constraints for unique data
- [ ] CHECK constraints for data validation
- [ ] Default values where appropriate
- [ ] Proper data types
- [ ] Timestamps (created_at, updated_at)
- [ ] Soft delete support (deleted_at) if needed

### 6. Add Indexes

```sql
-- Indexes for foreign keys (improves JOIN performance)
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);

-- Indexes for frequently queried columns
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_products_name ON products(name);
CREATE INDEX idx_orders_status ON orders(status);

-- Composite index for common query patterns
CREATE INDEX idx_orders_user_status ON orders(user_id, status);

-- Partial index (PostgreSQL) for active orders only
CREATE INDEX idx_active_orders ON orders(created_at) 
WHERE deleted_at IS NULL;

-- Full-text search index (if needed)
CREATE INDEX idx_products_search ON products 
USING GIN(to_tsvector('english', name || ' ' || description));
```

**Index Strategy:**
- [ ] Index all foreign keys
- [ ] Index frequently searched columns (WHERE clauses)
- [ ] Index columns used in ORDER BY
- [ ] Composite indexes for multi-column queries
- [ ] Avoid over-indexing (indexes slow down writes)
- [ ] Monitor query performance and add indexes as needed

### 7. Define Constraints and Triggers

```sql
-- Constraint: Ensure order total matches sum of order items
CREATE OR REPLACE FUNCTION check_order_total()
RETURNS TRIGGER AS $$
DECLARE
  calculated_total DECIMAL(10, 2);
BEGIN
  SELECT COALESCE(SUM(quantity * price), 0)
  INTO calculated_total
  FROM order_items
  WHERE order_id = NEW.order_id;
  
  UPDATE orders
  SET total = calculated_total
  WHERE id = NEW.order_id;
  
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_order_total
AFTER INSERT OR UPDATE OR DELETE ON order_items
FOR EACH ROW
EXECUTE FUNCTION check_order_total();

-- Trigger: Update updated_at timestamp
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER users_updated_at
BEFORE UPDATE ON users
FOR EACH ROW
EXECUTE FUNCTION update_updated_at();
```

### 8. Create Migration Script

```sql
-- migrations/001_create_ecommerce_tables.up.sql

BEGIN;

-- Create tables
CREATE TABLE users (...);
CREATE TABLE products (...);
CREATE TABLE orders (...);
CREATE TABLE order_items (...);

-- Create indexes
CREATE INDEX idx_orders_user_id ON orders(user_id);
-- ...more indexes...

-- Create constraints and triggers
CREATE FUNCTION check_order_total() ...;
CREATE TRIGGER update_order_total ...;

COMMIT;
```

```sql
-- migrations/001_create_ecommerce_tables.down.sql

BEGIN;

DROP TRIGGER IF EXISTS update_order_total ON order_items;
DROP FUNCTION IF EXISTS check_order_total();

DROP TABLE IF EXISTS order_items CASCADE;
DROP TABLE IF EXISTS orders CASCADE;
DROP TABLE IF EXISTS products CASCADE;
DROP TABLE IF EXISTS users CASCADE;

COMMIT;
```

**Migration Checklist:**
- [ ] Transactional (BEGIN/COMMIT)
- [ ] Idempotent (can run multiple times safely)
- [ ] Tested rollback (down migration)
- [ ] Version controlled
- [ ] Documented changes

### 9. Document the Schema

```markdown
# E-commerce Database Schema

## Tables

### users
Stores user account information.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique user identifier |
| email | VARCHAR(255) | NOT NULL, UNIQUE | User email address |
| password_hash | VARCHAR(255) | NOT NULL | Hashed password |
| name | VARCHAR(100) | NOT NULL | User's full name |
| created_at | TIMESTAMP | NOT NULL | Account creation time |
| updated_at | TIMESTAMP | NOT NULL | Last update time |
| deleted_at | TIMESTAMP | NULL | Soft delete timestamp |

**Indexes:**
- `idx_users_email` on `email`

### orders
Stores customer orders.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique order identifier |
| user_id | UUID | FK → users.id | Order owner |
| total | DECIMAL(10,2) | NOT NULL, >= 0 | Order total amount |
| status | VARCHAR(50) | NOT NULL | Order status |
| created_at | TIMESTAMP | NOT NULL | Order creation time |
| updated_at | TIMESTAMP | NOT NULL | Last update time |

**Indexes:**
- `idx_orders_user_id` on `user_id`
- `idx_orders_status` on `status`

## Relationships

- `users` 1:N `orders` - One user can have many orders
- `orders` 1:N `order_items` - One order contains many items
- `products` 1:N `order_items` - One product can appear in many orders

## Common Queries

\`\`\`sql
-- Get user's recent orders
SELECT * FROM orders 
WHERE user_id = $1 
ORDER BY created_at DESC 
LIMIT 10;

-- Get order with items
SELECT 
  o.*,
  json_agg(oi.*) as items
FROM orders o
LEFT JOIN order_items oi ON o.id = oi.order_id
WHERE o.id = $1
GROUP BY o.id;
\`\`\`
```

## Best Practices

1. **Use UUIDs**: For distributed systems and security
2. **Timestamps**: Always include created_at, updated_at
3. **Soft Deletes**: Use deleted_at for recoverable deletions
4. **Constraints**: Enforce data integrity at database level
5. **Normalization**: Reduce redundancy, improve consistency
6. **Indexes**: Optimize for read patterns, but don't over-index
7. **Foreign Keys**: Maintain referential integrity
8. **Migrations**: Always version control and test rollback

## Data Type Recommendations

| Use Case | PostgreSQL | MySQL | SQL Server |
|----------|------------|-------|------------|
| Primary Key | UUID | UUID/BIGINT | UNIQUEIDENTIFIER |
| String | VARCHAR | VARCHAR | NVARCHAR |
| Long Text | TEXT | TEXT | NVARCHAR(MAX) |
| Integer | INTEGER | INT | INT |
| Decimal | DECIMAL(10,2) | DECIMAL(10,2) | DECIMAL(10,2) |
| Boolean | BOOLEAN | TINYINT(1) | BIT |
| Timestamp | TIMESTAMP | TIMESTAMP | DATETIME2 |
| JSON | JSONB | JSON | NVARCHAR(MAX) |

## Common Pitfalls to Avoid

❌ **Don't** use VARCHAR(255) for everything  
❌ **Don't** forget indexes on foreign keys  
❌ **Don't** store JSON in SQL when relations are clear  
❌ **Don't** use FLOAT for money (use DECIMAL)  
❌ **Don't** forget ON DELETE/ON UPDATE actions  
❌ **Don't** skip normalization without good reason  

## Related Skills

- `run_migration` - Execute database migrations
- `create_api_endpoint` - Create APIs that use this schema

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luanps2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
