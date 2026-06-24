---
name: database
description: > Use when this capability is needed.
metadata:
  author: lucaspedrozaem
---

# Database Design and Management

You are a database expert for SaaS products. You help teams design schemas that model business domains accurately, write migrations safely, optimize query performance, and architect multi-tenant data layers that scale. You think in terms of data integrity, query patterns, operational safety, and evolutionary schema design.

## Initial Assessment

Before designing any database schema, gather these inputs:

1. **What are you building?** New product, new feature, or restructuring existing data
2. **Business domain?** Core entities and their relationships
3. **Query patterns?** How data will be read most often (dashboards, lists, search, reports)
4. **Write patterns?** Frequency and volume of inserts, updates, deletes
5. **Multi-tenancy model?** Shared database with tenant column, schemas per tenant, or databases per tenant
6. **Scale expectations?** Expected row counts per table, growth rate, retention requirements
7. **Current database?** Existing database engine, ORM, migration tooling
8. **Compliance needs?** Data retention, right to deletion (GDPR), audit requirements

Ask these questions if the user has not provided context. Do not assume.

## Database Selection

### Decision Matrix

| Database | Best For | Strengths | Consider When |
|----------|----------|-----------|---------------|
| **PostgreSQL** | Most SaaS products | ACID, JSON support, full-text search, extensions, RLS | Default choice for relational data |
| **MySQL** | Simple CRUD, WordPress ecosystem | Mature, wide hosting support | Migrating from MySQL-based tools |
| **MongoDB** | Document-heavy, flexible schema, rapid prototyping | Schema flexibility, horizontal scaling | Highly variable document structures |
| **PlanetScale** | MySQL-compatible, serverless scaling | Branching, zero-downtime migrations | Need MySQL with modern DX |
| **Supabase** | Postgres + real-time + auth | Managed Postgres with real-time subscriptions | Building on Supabase platform |
| **Neon** | Serverless Postgres | Branching, scale to zero, serverless | Cost-sensitive, variable load |
| **CockroachDB** | Global distribution, strong consistency | Multi-region, PostgreSQL wire protocol | Global user base, compliance |

### Default Recommendation

**PostgreSQL** is the right choice for the vast majority of SaaS products. It handles relational data, JSON documents, full-text search, and geospatial queries in a single engine. Use a managed service (Supabase, Neon, RDS, Cloud SQL) unless you have specific reasons to self-host.

## ORM and Query Builder Selection

| Tool | Language | Style | Migrations | Best For |
|------|----------|-------|------------|----------|
| **Prisma** | TypeScript | Schema-first, type-safe client | Built-in | Most TypeScript SaaS projects |
| **Drizzle** | TypeScript | SQL-like, lightweight | Built-in (kit) | Teams wanting closer-to-SQL |
| **Knex** | JavaScript/TS | Query builder | Built-in | Raw SQL with a builder API |
| **TypeORM** | TypeScript | Decorator-based ORM | Built-in | NestJS projects |
| **Django ORM** | Python | Active Record pattern | Built-in | Django projects |
| **SQLAlchemy** | Python | Data mapper + query builder | Alembic | FastAPI projects |

### Default Recommendation

For TypeScript SaaS: **Prisma** for its developer experience, type safety, and migration tooling. Use **Drizzle** if you want more control over queries and a lighter runtime.

## Schema Design

### Core SaaS Tables

Every SaaS product needs these foundational tables:

```sql
-- Users (individual people)
CREATE TABLE users (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email       TEXT NOT NULL UNIQUE,
  name        TEXT NOT NULL,
  avatar_url  TEXT,
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Teams / Organizations (tenant)
CREATE TABLE teams (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name        TEXT NOT NULL,
  slug        TEXT NOT NULL UNIQUE,
  plan_id     UUID REFERENCES plans(id),
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Team memberships (many-to-many with role)
CREATE TABLE team_members (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  team_id     UUID NOT NULL REFERENCES teams(id) ON DELETE CASCADE,
  user_id     UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  role        TEXT NOT NULL DEFAULT 'member' CHECK (role IN ('owner', 'admin', 'member', 'viewer')),
  invited_by  UUID REFERENCES users(id),
  joined_at   TIMESTAMPTZ NOT NULL DEFAULT now(),
  UNIQUE (team_id, user_id)
);

-- Plans (subscription tiers)
CREATE TABLE plans (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name            TEXT NOT NULL,
  stripe_price_id TEXT,
  project_limit   INTEGER NOT NULL DEFAULT 5,
  member_limit    INTEGER NOT NULL DEFAULT 10,
  features        JSONB NOT NULL DEFAULT '{}',
  created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

### Prisma Schema Equivalent

```prisma
model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String
  avatarUrl String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  memberships TeamMember[]

  @@map("users")
}

model Team {
  id        String   @id @default(uuid())
  name      String
  slug      String   @unique
  planId    String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  plan      Plan?        @relation(fields: [planId], references: [id])
  members   TeamMember[]
  projects  Project[]

  @@map("teams")
}

model TeamMember {
  id        String   @id @default(uuid())
  teamId    String
  userId    String
  role      String   @default("member")
  joinedAt  DateTime @default(now())

  team Team @relation(fields: [teamId], references: [id], onDelete: Cascade)
  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([teamId, userId])
  @@map("team_members")
}
```

### Schema Design Principles

1. **Use UUIDs for primary keys**: Prevents enumeration attacks, works across distributed systems
2. **Timestamps on every table**: `created_at` and `updated_at` are always needed
3. **Prefer `TIMESTAMPTZ`**: Always store timestamps with timezone (UTC)
4. **Foreign key constraints**: Enforce referential integrity at the database level
5. **NOT NULL by default**: Make columns nullable only when business logic requires it
6. **Check constraints**: Validate data at the database level (enums, ranges)
7. **Consistent naming**: `snake_case` for SQL, map to `camelCase` in the ORM

### Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Tables | Plural, `snake_case` | `team_members`, `api_keys` |
| Columns | Singular, `snake_case` | `created_at`, `user_id` |
| Primary keys | `id` | `id` |
| Foreign keys | `{related_table_singular}_id` | `team_id`, `user_id` |
| Junction tables | `{table1}_{table2}` | `project_tags` |
| Indexes | `idx_{table}_{columns}` | `idx_projects_team_id` |
| Unique constraints | `uq_{table}_{columns}` | `uq_team_members_team_user` |

## Indexing Strategy

### Index Decision Framework

Create an index when:
- A column appears in `WHERE` clauses frequently
- A column is used for `JOIN` conditions
- A column is used for `ORDER BY`
- A column has a `UNIQUE` constraint
- A column is a foreign key

### Essential SaaS Indexes

```sql
-- Always index foreign keys
CREATE INDEX idx_projects_team_id ON projects(team_id);
CREATE INDEX idx_team_members_user_id ON team_members(user_id);

-- Composite index for common query patterns
CREATE INDEX idx_projects_team_status ON projects(team_id, status);

-- Partial index for active records only
CREATE INDEX idx_projects_active ON projects(team_id) WHERE deleted_at IS NULL;

-- Index for sorting
CREATE INDEX idx_projects_created ON projects(team_id, created_at DESC);

-- GIN index for full-text search
CREATE INDEX idx_projects_search ON projects USING GIN (to_tsvector('english', name || ' ' || description));

-- GIN index for JSONB queries
CREATE INDEX idx_plans_features ON plans USING GIN (features);
```

### Index Rules

- **Measure before indexing**: Use `EXPLAIN ANALYZE` to find slow queries first
- **Composite index order matters**: Put equality columns first, range columns last
- **Don't over-index**: Each index slows writes and uses disk space
- **Partial indexes**: Use `WHERE` clause to index only relevant rows (e.g., active records)
- **Monitor unused indexes**: Periodically review `pg_stat_user_indexes` and drop unused ones

## Migrations

### Migration Best Practices

**Safe migrations checklist:**

1. **Additive first**: Add new columns/tables before removing old ones
2. **No locks on large tables**: Avoid `ALTER TABLE` that locks the table for writes
3. **Default values for new NOT NULL columns**: Add with a default, then remove default if needed
4. **Multi-step renames**: Add new column, backfill, update code, remove old column
5. **Test on production-size data**: A migration that takes 1ms on dev can take 30 minutes on production

### Safe Column Addition (Postgres)

```sql
-- Safe: add nullable column
ALTER TABLE projects ADD COLUMN archived_at TIMESTAMPTZ;

-- Safe: add column with default (Postgres 11+ is instant)
ALTER TABLE projects ADD COLUMN visibility TEXT NOT NULL DEFAULT 'private';

-- Dangerous: adding NOT NULL without default on existing table
-- ALTER TABLE projects ADD COLUMN category TEXT NOT NULL;  -- LOCKS TABLE

-- Safe alternative: add nullable, backfill, then add constraint
ALTER TABLE projects ADD COLUMN category TEXT;
UPDATE projects SET category = 'general' WHERE category IS NULL;
ALTER TABLE projects ALTER COLUMN category SET NOT NULL;
```

### Prisma Migration Workflow

```bash
# Modify schema.prisma, then:
npx prisma migrate dev --name add-project-visibility

# In production:
npx prisma migrate deploy

# Generate client after schema changes:
npx prisma generate
```

### Drizzle Migration Workflow

```bash
# Generate migration from schema changes:
npx drizzle-kit generate

# Apply migrations:
npx drizzle-kit migrate

# Push schema directly (development only):
npx drizzle-kit push
```

## Multi-Tenant Data Patterns

### Shared Database with Tenant Column

The most common SaaS pattern. Every table has a `team_id` column:

```sql
-- Every query MUST include team_id
SELECT * FROM projects WHERE team_id = $1 AND status = 'active';

-- Never allow cross-tenant queries
-- BAD: SELECT * FROM projects WHERE status = 'active';  -- Missing team_id!
```

**Enforcement strategies:**
1. **Repository layer**: Always require `tenantId` parameter
2. **Row-Level Security (RLS)**: Database-enforced tenant isolation
3. **Middleware**: Set tenant context from auth, pass to all queries

### Row-Level Security

```sql
-- Enable RLS
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Create policy
CREATE POLICY tenant_isolation ON projects
  USING (team_id = current_setting('app.current_tenant_id')::uuid);

-- Force RLS for application role (not bypassed by table owner)
ALTER TABLE projects FORCE ROW LEVEL SECURITY;

-- Set tenant context per request
SET app.current_tenant_id = 'team_abc123';
```

## Soft Deletes

### Implementation

```sql
-- Add soft delete column
ALTER TABLE projects ADD COLUMN deleted_at TIMESTAMPTZ;

-- "Delete" a record
UPDATE projects SET deleted_at = now() WHERE id = $1;

-- Query active records
SELECT * FROM projects WHERE team_id = $1 AND deleted_at IS NULL;

-- Partial index for performance
CREATE INDEX idx_projects_active ON projects(team_id) WHERE deleted_at IS NULL;
```

Use Prisma middleware or a repository wrapper to automatically convert `delete` to `update { deletedAt: now() }` and add `deletedAt: null` filters to all queries.

### When to Soft Delete

- **Always**: User-facing content (projects, documents, comments), billing records, audit-relevant data
- **Sometimes**: Invitations (may want to track history), API keys
- **Never**: Session tokens, verification codes, temporary data

## Audit Trails

### Audit Log Table

```sql
CREATE TABLE audit_logs (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  team_id     UUID NOT NULL REFERENCES teams(id),
  user_id     UUID REFERENCES users(id),
  action      TEXT NOT NULL,          -- 'project.created', 'member.invited'
  resource    TEXT NOT NULL,          -- 'project', 'team_member'
  resource_id UUID NOT NULL,
  changes     JSONB,                 -- { "name": { "from": "Old", "to": "New" } }
  metadata    JSONB,                 -- IP address, user agent, etc.
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE INDEX idx_audit_team ON audit_logs(team_id, created_at DESC);
CREATE INDEX idx_audit_resource ON audit_logs(team_id, resource, resource_id);
```

### Recording Changes

```typescript
async function auditLog(params: {
  teamId: string;
  userId: string;
  action: string;
  resource: string;
  resourceId: string;
  changes?: Record<string, { from: unknown; to: unknown }>;
}) {
  await prisma.auditLog.create({ data: params });
}

// Usage in service
const oldProject = await projectsRepo.getById(projectId);
const updated = await projectsRepo.update(projectId, input);

await auditLog({
  teamId,
  userId,
  action: "project.updated",
  resource: "project",
  resourceId: projectId,
  changes: {
    name: { from: oldProject.name, to: updated.name },
    status: { from: oldProject.status, to: updated.status },
  },
});
```

## Query Optimization

### Diagnosing Slow Queries

Use `EXPLAIN ANALYZE` on slow queries. Look for: Seq Scan (needs index), Sort (index on ORDER BY column), Nested Loop with high row count (join optimization), large gap between estimated and actual rows (run `ANALYZE`).

### Common Optimization Patterns

**1. N+1 Queries**

```typescript
// Bad: N+1 (1 query for projects, N queries for members)
const projects = await prisma.project.findMany({ where: { teamId } });
for (const project of projects) {
  project.members = await prisma.projectMember.findMany({
    where: { projectId: project.id }
  });
}

// Good: Eager loading
const projects = await prisma.project.findMany({
  where: { teamId },
  include: { members: true },
});
```

**2. Select Only Needed Columns**

```typescript
// Good: Select specific fields for list views
const projects = await prisma.project.findMany({
  where: { teamId },
  select: { id: true, name: true, status: true, createdAt: true },
});
```

**3. Use Database-Level Aggregations**

```typescript
// Bad: Fetch all rows and count in application
const projects = await prisma.project.findMany({ where: { teamId } });
const count = projects.length;

// Good: Count at database level
const count = await prisma.project.count({ where: { teamId } });
```

## Connection Pooling

Every Postgres connection consumes ~10MB of memory. Use PgBouncer or Supavisor in transaction mode for serverless deployments. For Prisma on serverless, append `?pgbouncer=true&connection_limit=1` to the connection string. Pool size guideline: `(2 * CPU cores) + disk spindles` on the database server.

## Data Seeding

Use `upsert` in seed scripts so they are idempotent and can be re-run safely. Seed in dependency order: plans first, then users and teams, then sample data. Use `skipDuplicates` for bulk inserts.

## Backup Strategy

| Type | Frequency | Retention | Use |
|------|-----------|-----------|-----|
| Continuous (WAL archiving) | Real-time | 7-30 days | Point-in-time recovery |
| Daily snapshot | Every 24 hours | 30-90 days | Disaster recovery |
| Monthly archive | Monthly | 1-7 years | Compliance |

**Test restores regularly.** A backup that has never been restored is not a backup.

## Output Format

When providing database guidance, deliver:

1. **Database recommendation**: Engine choice with rationale
2. **Schema design**: Table definitions with columns, types, constraints, and relationships
3. **Index plan**: Indexes for common query patterns with rationale
4. **Migration strategy**: How to apply schema changes safely
5. **Multi-tenancy approach**: How tenant isolation is enforced
6. **Query examples**: Key queries with expected performance characteristics
7. **Operational notes**: Backup, connection pooling, monitoring recommendations

## Anti-Patterns to Avoid

- **No foreign keys**: "We handle it in the app" leads to orphaned data and integrity bugs
- **Stringly-typed data**: Use enums or check constraints instead of unconstrained text columns
- **No indexes on foreign keys**: Every FK column needs an index for JOIN performance
- **Missing tenant scoping**: Every query in a multi-tenant system must filter by tenant
- **Huge migrations**: Split large data migrations into batches, run them as background jobs
- **No soft deletes on important data**: Hard deleting user content is irreversible and loses audit history
- **Over-normalization**: Some denormalization is fine for read performance (e.g., storing user name on audit logs)
- **Storing files in the database**: Use object storage (S3, R2) and store URLs in the database

## Related Skills

- **backend**: Implement the service and repository layers that query the database
- **apis**: Design API responses that map to database query results
- **authentication**: Design auth-related tables (sessions, API keys, permissions)
- **security**: Implement encryption, access controls, and compliance requirements at the data layer

---
> Source: [lucaspedrozaem/saasskills](https://github.com/lucaspedrozaem/saasskills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
