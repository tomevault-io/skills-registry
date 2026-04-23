---
name: postgres-best-practices
description: PostgreSQL performance optimization guidelines. Use when writing SQL queries, designing schemas, implementing indexes, reviewing database performance, configuring connection pooling, or working with Row-Level Security (RLS). Use when this capability is needed.
metadata:
  author: allanninal
---

# PostgreSQL Best Practices

## When to Use This Skill

- Writing SQL queries or designing schemas
- Implementing indexes or query optimization
- Reviewing database performance issues
- Configuring connection pooling or scaling
- Working with Row-Level Security (RLS)
- Supabase or any PostgreSQL database work

## Priority Categories

### 1. Query Performance (Critical)

```sql
-- BAD: SELECT * pulls unnecessary columns
SELECT * FROM users WHERE id = 1;

-- GOOD: Select only needed columns
SELECT id, name, email FROM users WHERE id = 1;

-- BAD: N+1 queries
SELECT * FROM orders WHERE user_id IN (SELECT id FROM users);

-- GOOD: Use JOINs
SELECT o.*, u.name
FROM orders o
JOIN users u ON o.user_id = u.id;
```

**Index Guidelines:**
- Create indexes on columns used in WHERE, JOIN, ORDER BY
- Use composite indexes for multi-column queries (order matters!)
- Prefer partial indexes for filtered queries
- Use EXPLAIN ANALYZE to verify index usage

```sql
-- Composite index (col1, col2) supports:
-- WHERE col1 = x
-- WHERE col1 = x AND col2 = y
-- ORDER BY col1, col2

-- But NOT:
-- WHERE col2 = y (without col1)
```

### 2. Connection Management (Critical)

**Connection Pooling:**
- Use PgBouncer or Supabase's built-in pooler
- Transaction pooling mode for serverless
- Session pooling for persistent connections
- Keep connections under pool limits

```typescript
// Serverless function best practice
const pool = new Pool({
  max: 10,           // Limit connections
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});
```

### 3. Schema Design (High)

**Normalization:**
- Follow 3NF unless performance requires denormalization
- Use proper data types (don't store numbers as text)
- Add constraints (NOT NULL, CHECK, FOREIGN KEY)

```sql
-- Use appropriate types
CREATE TABLE products (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  price NUMERIC(10,2) NOT NULL CHECK (price >= 0),
  quantity INTEGER NOT NULL DEFAULT 0 CHECK (quantity >= 0),
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

**Naming Conventions:**
- Tables: plural, snake_case (users, order_items)
- Columns: snake_case (created_at, user_id)
- Indexes: idx_table_column (idx_users_email)
- Constraints: table_column_type (users_email_unique)

### 4. Concurrency & Locking (Medium-High)

```sql
-- Use row-level locks appropriately
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;

-- Avoid long transactions
BEGIN;
  -- Keep transaction short!
COMMIT;

-- Use SKIP LOCKED for job queues
SELECT * FROM jobs
WHERE status = 'pending'
ORDER BY created_at
LIMIT 1
FOR UPDATE SKIP LOCKED;
```

### 5. Security & RLS (Medium-High)

```sql
-- Enable RLS
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- Create policies
CREATE POLICY "Users see own docs" ON documents
  FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users insert own docs" ON documents
  FOR INSERT
  WITH CHECK (auth.uid() = user_id);
```

**Security Checklist:**
- [ ] RLS enabled on all user-facing tables
- [ ] Parameterized queries (never string concatenation)
- [ ] Minimal privileges per role
- [ ] Audit logging for sensitive operations

### 6. Data Access Patterns (Medium)

**Pagination:**
```sql
-- BAD: OFFSET is slow for large pages
SELECT * FROM items LIMIT 20 OFFSET 10000;

-- GOOD: Cursor-based pagination
SELECT * FROM items
WHERE id > $last_seen_id
ORDER BY id
LIMIT 20;
```

**Batch Operations:**
```sql
-- Use bulk inserts
INSERT INTO items (name, value) VALUES
  ('a', 1), ('b', 2), ('c', 3);

-- Use COPY for large imports
COPY items FROM '/path/to/data.csv' WITH CSV HEADER;
```

### 7. Monitoring & Diagnostics (Low-Medium)

```sql
-- Find slow queries
SELECT query, calls, mean_exec_time, total_exec_time
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;

-- Check index usage
SELECT schemaname, tablename, indexname, idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0;

-- Monitor connections
SELECT count(*), state FROM pg_stat_activity GROUP BY state;
```

### 8. Advanced Features (Low)

**Full-Text Search:**
```sql
-- Create search index
CREATE INDEX idx_docs_search ON documents
USING GIN (to_tsvector('english', content));

-- Query with ranking
SELECT *, ts_rank(to_tsvector(content), query) AS rank
FROM documents, plainto_tsquery('search terms') query
WHERE to_tsvector('english', content) @@ query
ORDER BY rank DESC;
```

**JSON Operations:**
```sql
-- Store and query JSONB
SELECT data->>'name' AS name
FROM records
WHERE data @> '{"active": true}';
```

## Quick Reference

| Problem | Solution |
|---------|----------|
| Slow query | EXPLAIN ANALYZE, add indexes |
| Too many connections | Use connection pooler |
| N+1 queries | Use JOINs or batch loading |
| Large result sets | Cursor pagination |
| Concurrent updates | Row-level locking |
| Security | Enable RLS, use policies |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
