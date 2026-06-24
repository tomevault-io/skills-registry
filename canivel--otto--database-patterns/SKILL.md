---
name: database-patterns
description: Use when designing schemas, writing queries, or managing migrations with Drizzle ORM and PostgreSQL. Covers schema design, CRUD operations, joins, transactions, indexing, and connection pooling.
metadata:
  author: canivel
---

# Database Patterns with Drizzle ORM + PostgreSQL

## Schema Design

```ts
// db/schema/users.ts
import { pgTable, serial, varchar, timestamp, boolean, index } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  email: varchar('email', { length: 255 }).notNull().unique(),
  name: varchar('name', { length: 100 }).notNull(),
  passwordHash: varchar('password_hash', { length: 255 }).notNull(),
  role: varchar('role', { length: 20 }).notNull().default('user'),
  isActive: boolean('is_active').notNull().default(true),
  createdAt: timestamp('created_at').notNull().defaultNow(),
  updatedAt: timestamp('updated_at').notNull().defaultNow(),
}, (table) => ({
  emailIdx: index('users_email_idx').on(table.email),
  roleIdx: index('users_role_idx').on(table.role),
}));

// Relations
export const projects = pgTable('projects', {
  id: serial('id').primaryKey(),
  name: varchar('name', { length: 200 }).notNull(),
  ownerId: serial('owner_id').references(() => users.id, { onDelete: 'cascade' }),
  createdAt: timestamp('created_at').notNull().defaultNow(),
});

export const tasks = pgTable('tasks', {
  id: serial('id').primaryKey(),
  title: varchar('title', { length: 300 }).notNull(),
  status: varchar('status', { length: 20 }).notNull().default('todo'),
  projectId: serial('project_id').references(() => projects.id, { onDelete: 'cascade' }),
  assigneeId: serial('assignee_id').references(() => users.id),
});
```

## Database Connection

```ts
// db/index.ts
import { drizzle } from 'drizzle-orm/node-postgres';
import { Pool } from 'pg';
import * as schema from './schema';

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 20,               // max connections in pool
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 5000,
});

export const db = drizzle(pool, { schema });
```

## Migrations

```bash
# Generate migration from schema changes
npx drizzle-kit generate

# Apply migrations
npx drizzle-kit migrate

# Push schema directly (development only)
npx drizzle-kit push
```

```ts
// drizzle.config.ts
import { defineConfig } from 'drizzle-kit';

export default defineConfig({
  schema: './src/db/schema/*',
  out: './drizzle',
  dialect: 'postgresql',
  dbCredentials: { url: process.env.DATABASE_URL! },
});
```

## CRUD Operations

```ts
import { eq, and, ilike, desc, sql } from 'drizzle-orm';

// SELECT
const allUsers = await db.select().from(users);
const activeAdmins = await db.select().from(users)
  .where(and(eq(users.role, 'admin'), eq(users.isActive, true)));

// SELECT specific columns
const names = await db.select({ id: users.id, name: users.name }).from(users);

// INSERT single
const [newUser] = await db.insert(users)
  .values({ email: 'alice@example.com', name: 'Alice', passwordHash: hash })
  .returning();

// INSERT multiple
await db.insert(tasks).values([
  { title: 'Task 1', projectId: 1 },
  { title: 'Task 2', projectId: 1 },
]);

// UPDATE
const [updated] = await db.update(users)
  .set({ name: 'Alice Smith', updatedAt: new Date() })
  .where(eq(users.id, 1))
  .returning();

// DELETE
await db.delete(tasks).where(eq(tasks.id, 5));

// UPSERT
await db.insert(users)
  .values({ email: 'alice@example.com', name: 'Alice', passwordHash: hash })
  .onConflictDoUpdate({
    target: users.email,
    set: { name: 'Alice', updatedAt: new Date() },
  });
```

## Joins

```ts
// Inner join
const projectsWithOwners = await db
  .select({
    project: projects,
    owner: { id: users.id, name: users.name },
  })
  .from(projects)
  .innerJoin(users, eq(projects.ownerId, users.id));

// Left join with tasks
const projectsWithTasks = await db
  .select()
  .from(projects)
  .leftJoin(tasks, eq(tasks.projectId, projects.id))
  .where(eq(projects.ownerId, userId));

// Using relational queries (requires relations defined)
const result = await db.query.projects.findMany({
  where: eq(projects.ownerId, userId),
  with: {
    tasks: { where: eq(tasks.status, 'todo') },
    owner: true,
  },
});
```

## Transactions

```ts
// Use transactions for multi-step operations that must be atomic
const newProject = await db.transaction(async (tx) => {
  const [project] = await tx.insert(projects)
    .values({ name: 'New Project', ownerId: userId })
    .returning();

  await tx.insert(tasks).values([
    { title: 'Initial setup', projectId: project.id, assigneeId: userId },
    { title: 'Write README', projectId: project.id, assigneeId: userId },
  ]);

  await tx.update(users)
    .set({ updatedAt: new Date() })
    .where(eq(users.id, userId));

  return project;
});

// Transaction with rollback on validation failure
await db.transaction(async (tx) => {
  const [user] = await tx.select().from(users).where(eq(users.id, userId));
  if (user.role !== 'admin') {
    tx.rollback();
    return;
  }
  await tx.delete(projects).where(eq(projects.id, projectId));
});
```

## Indexing Guidelines

```ts
// Index columns used in WHERE clauses and JOINs
// Index columns used in ORDER BY
// Use composite indexes for queries that filter on multiple columns
pgTable('tasks', { ... }, (table) => ({
  projectStatusIdx: index('tasks_project_status_idx').on(table.projectId, table.status),
  assigneeIdx: index('tasks_assignee_idx').on(table.assigneeId),
}));

// NEVER index columns with very low cardinality (e.g., boolean flags on small tables)
// NEVER create redundant indexes that duplicate the leading columns of existing indexes
```

## Anti-Patterns

- NEVER use `SELECT *` in production queries with joins. Select only needed columns.
- NEVER run migrations automatically on application startup in production.
- NEVER store passwords as plain text. Always hash with bcrypt/argon2.
- NEVER use string concatenation to build queries. Always use Drizzle's query builder.
- NEVER skip connection pooling. Always use a Pool, never individual Client connections.
- NEVER fetch all rows when you only need a count or existence check. Use `sql<number>\`count(*)\`` or `LIMIT 1`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
