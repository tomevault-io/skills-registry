---
name: neon-database
description: Neon serverless Postgres - branching, connection pooling, autoscaling, and best practices. Use when working with Neon database or serverless Postgres. Use when this capability is needed.
metadata:
  author: allanninal
---

# Neon Serverless Postgres

## When to Use This Skill

- Setting up Neon database connections
- Using database branching for development
- Configuring connection pooling
- Optimizing for serverless environments
- Integrating Neon with frameworks

## Quick Start

### Create Project

```bash
# Using Neon CLI
npm install -g neonctl
neonctl auth

# Create project
neonctl projects create --name my-project

# Get connection string
neonctl connection-string
```

### Connection String Format

```
postgresql://[user]:[password]@[endpoint].neon.tech/[database]?sslmode=require
```

## Connection Patterns

### Serverless Function (Recommended)

```typescript
// Use Neon serverless driver for edge/serverless
import { neon } from '@neondatabase/serverless';

const sql = neon(process.env.DATABASE_URL!);

export async function getUsers() {
  const users = await sql`SELECT * FROM users LIMIT 10`;
  return users;
}
```

### Connection Pooling with Drizzle

```typescript
import { drizzle } from 'drizzle-orm/neon-http';
import { neon } from '@neondatabase/serverless';

const sql = neon(process.env.DATABASE_URL!);
export const db = drizzle(sql);

// Usage
const users = await db.select().from(usersTable).limit(10);
```

### Node.js with pg (Long-running)

```typescript
import { Pool } from 'pg';

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: { rejectUnauthorized: true },
  max: 10,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 10000,
});

// Use pooled connections
const client = await pool.connect();
try {
  const result = await client.query('SELECT * FROM users');
  return result.rows;
} finally {
  client.release();
}
```

### Prisma Integration

```prisma
// prisma/schema.prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL") // For migrations
}
```

```typescript
// For edge/serverless
import { PrismaClient } from '@prisma/client'
import { PrismaNeon } from '@prisma/adapter-neon'
import { neon } from '@neondatabase/serverless'

const sql = neon(process.env.DATABASE_URL!)
const adapter = new PrismaNeon(sql)
const prisma = new PrismaClient({ adapter })
```

## Database Branching

### Create Development Branch

```bash
# Create branch from main
neonctl branches create --name dev --parent main

# Get branch connection string
neonctl connection-string --branch dev

# List branches
neonctl branches list

# Delete branch
neonctl branches delete dev
```

### Branch Workflow

```markdown
## Development Workflow

1. **Main Branch**: Production data
2. **Dev Branch**: Daily development
3. **Feature Branches**: Per-feature isolation

```
main (production)
├── dev (shared development)
│   ├── feature/auth
│   └── feature/payments
└── staging (pre-production)
```

### CI/CD Branch Strategy

```yaml
# .github/workflows/preview.yml
name: Preview Environment
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  create-preview:
    runs-on: ubuntu-latest
    steps:
      - name: Create Neon Branch
        uses: neondatabase/create-branch-action@v4
        with:
          project_id: ${{ secrets.NEON_PROJECT_ID }}
          branch_name: preview-${{ github.event.pull_request.number }}
          api_key: ${{ secrets.NEON_API_KEY }}

      - name: Run Migrations
        run: npx prisma migrate deploy
        env:
          DATABASE_URL: ${{ steps.create-preview.outputs.db_url }}
```

## Autoscaling Configuration

### Compute Settings

```typescript
// Neon autoscales based on load
// Configure min/max compute units in dashboard

// For predictable workloads, set minimum compute
// Project Settings > Compute > Minimum compute size

// Scale to zero saves costs during inactivity
// Re-activation takes ~500ms (cold start)
```

### Handle Cold Starts

```typescript
// Warm connection on app start
async function warmDatabase() {
  const sql = neon(process.env.DATABASE_URL!);
  await sql`SELECT 1`;
}

// Call during initialization
warmDatabase().catch(console.error);
```

## Best Practices

### Query Optimization

```sql
-- Use EXPLAIN ANALYZE
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';

-- Add appropriate indexes
CREATE INDEX idx_users_email ON users(email);

-- Use partial indexes for common filters
CREATE INDEX idx_active_users ON users(created_at)
WHERE status = 'active';
```

### Connection Management

```typescript
// DON'T: Create new connection per request
async function handleRequest() {
  const pool = new Pool({ connectionString: url }); // BAD
  const result = await pool.query('SELECT 1');
  await pool.end();
}

// DO: Reuse connection pool
const pool = new Pool({ connectionString: url }); // Create once

async function handleRequest() {
  const result = await pool.query('SELECT 1');
  return result;
}
```

### Serverless Considerations

```typescript
// For serverless: Use HTTP-based driver
import { neon } from '@neondatabase/serverless';
const sql = neon(process.env.DATABASE_URL!);

// For long-running: Use WebSocket for better performance
import { neonConfig, Pool } from '@neondatabase/serverless';
import ws from 'ws';

neonConfig.webSocketConstructor = ws;
const pool = new Pool({ connectionString: url });
```

## Environment Setup

### Next.js

```env
# .env.local
DATABASE_URL="postgresql://user:pass@ep-xxx.region.aws.neon.tech/dbname?sslmode=require"

# For Prisma migrations (bypasses pooler)
DIRECT_URL="postgresql://user:pass@ep-xxx.region.aws.neon.tech/dbname?sslmode=require"
```

### Vercel Integration

```bash
# Connect Neon to Vercel
# 1. Go to Neon Dashboard > Integrations
# 2. Select Vercel
# 3. Authorize and select project
# Environment variables are set automatically
```

## Monitoring

### Built-in Metrics

```markdown
## Available in Neon Console

- Connection count
- Query performance
- Storage usage
- Compute hours
- Data transfer
```

### Query Performance

```sql
-- Enable query statistics
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;

-- Find slow queries
SELECT
  query,
  calls,
  mean_exec_time,
  total_exec_time
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;
```

## Schema Management

### Migrations with Prisma

```bash
# Development
npx prisma migrate dev --name add_users

# Production
npx prisma migrate deploy
```

### Migrations with Drizzle

```bash
# Generate migration
npx drizzle-kit generate:pg

# Apply migration
npx drizzle-kit push:pg
```

## Checklist

- [ ] Use serverless driver for edge functions
- [ ] Configure connection pooling appropriately
- [ ] Set up database branching for dev/staging
- [ ] Add indexes for common queries
- [ ] Monitor query performance
- [ ] Use SSL connections (sslmode=require)
- [ ] Configure autoscaling limits
- [ ] Handle cold starts gracefully

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
