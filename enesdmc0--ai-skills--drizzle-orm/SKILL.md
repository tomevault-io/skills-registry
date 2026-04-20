---
name: drizzle-orm
description: This skill should be used when the user asks to \"create a database schema\", \"add a table\", \"write a migration\", \"query the database\", \"add a relation\", \"setup Drizzle\", or is working with Drizzle ORM, database models, and SQL operations in a TypeScript project. Use when this capability is needed.
metadata:
  author: enesdmc0
---

# Drizzle ORM Standards

## Overview

Standards for using Drizzle ORM with PostgreSQL (Supabase) in Next.js projects. Covers schema definition, queries, relations, and migrations.

## Project Structure

```
src/db/
├── index.ts              # DB client export
├── schema/               # Table definitions (one file per table)
│   ├── users.ts
│   ├── posts.ts
│   └── comments.ts
├── relations.ts          # All relations defined here
└── migrations/           # Generated SQL migrations
```

## DB Client Setup

File: `src/db/index.ts`

```typescript
import { drizzle } from "drizzle-orm/postgres-js"
import postgres from "postgres"
import * as schema from "./schema"

const connectionString = process.env.DATABASE_URL!
const client = postgres(connectionString)

export const db = drizzle(client, { schema })
```

## Schema Definition

### Basic Table

File: `src/db/schema/users.ts`

```typescript
import { pgTable, text, timestamp, uuid, boolean } from "drizzle-orm/pg-core"

export const users = pgTable("users", {
  id: uuid("id").primaryKey().defaultRandom(),
  name: text("name").notNull(),
  email: text("email").notNull().unique(),
  avatarUrl: text("avatar_url"),
  isActive: boolean("is_active").notNull().default(true),
  createdAt: timestamp("created_at", { withTimezone: true }).notNull().defaultNow(),
  updatedAt: timestamp("updated_at", { withTimezone: true }).notNull().defaultNow(),
})

export type User = typeof users.$inferSelect
export type NewUser = typeof users.$inferInsert
```

### Table with Foreign Key

File: `src/db/schema/posts.ts`

```typescript
import { pgTable, text, timestamp, uuid, boolean } from "drizzle-orm/pg-core"
import { users } from "./users"

export const posts = pgTable("posts", {
  id: uuid("id").primaryKey().defaultRandom(),
  title: text("title").notNull(),
  content: text("content").notNull(),
  published: boolean("published").notNull().default(false),
  authorId: uuid("author_id").notNull().references(() => users.id, { onDelete: "cascade" }),
  createdAt: timestamp("created_at", { withTimezone: true }).notNull().defaultNow(),
  updatedAt: timestamp("updated_at", { withTimezone: true }).notNull().defaultNow(),
})

export type Post = typeof posts.$inferSelect
export type NewPost = typeof posts.$inferInsert
```

### Enum Type

```typescript
import { pgEnum } from "drizzle-orm/pg-core"

export const roleEnum = pgEnum("role", ["admin", "user", "moderator"])

// Usage in table
export const users = pgTable("users", {
  // ...
  role: roleEnum("role").notNull().default("user"),
})
```

### Index and Constraints

```typescript
import { pgTable, text, uuid, index, uniqueIndex } from "drizzle-orm/pg-core"

export const posts = pgTable("posts", {
  id: uuid("id").primaryKey().defaultRandom(),
  slug: text("slug").notNull(),
  authorId: uuid("author_id").notNull(),
}, (table) => [
  index("posts_author_id_idx").on(table.authorId),
  uniqueIndex("posts_slug_idx").on(table.slug),
])
```

## Relations

File: `src/db/relations.ts`

```typescript
import { relations } from "drizzle-orm"
import { users } from "./schema/users"
import { posts } from "./schema/posts"
import { comments } from "./schema/comments"

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
  comments: many(comments),
}))

export const postsRelations = relations(posts, ({ one, many }) => ({
  author: one(users, {
    fields: [posts.authorId],
    references: [users.id],
  }),
  comments: many(comments),
}))

export const commentsRelations = relations(comments, ({ one }) => ({
  post: one(posts, {
    fields: [comments.postId],
    references: [posts.id],
  }),
  author: one(users, {
    fields: [comments.authorId],
    references: [users.id],
  }),
}))
```

## Query Patterns

### Select with Filter

```typescript
import { db } from "@/db"
import { posts } from "@/db/schema/posts"
import { eq, and, desc, like, sql } from "drizzle-orm"

// Basic select
const allPosts = await db.select().from(posts)

// With filter
const publishedPosts = await db
  .select()
  .from(posts)
  .where(eq(posts.published, true))
  .orderBy(desc(posts.createdAt))

// Multiple conditions
const userPublished = await db
  .select()
  .from(posts)
  .where(and(eq(posts.authorId, userId), eq(posts.published, true)))

// Pagination
const paginated = await db
  .select()
  .from(posts)
  .limit(10)
  .offset(page * 10)

// Search
const searched = await db
  .select()
  .from(posts)
  .where(like(posts.title, `%${query}%`))
```

### Relational Queries (Recommended)

```typescript
// Fetch post with author
const postWithAuthor = await db.query.posts.findFirst({
  where: eq(posts.id, postId),
  with: {
    author: true,
    comments: {
      with: { author: true },
      orderBy: [desc(comments.createdAt)],
      limit: 10,
    },
  },
})

// Fetch all users with post count
const usersWithPosts = await db.query.users.findMany({
  with: {
    posts: {
      where: eq(posts.published, true),
    },
  },
})
```

### Insert

```typescript
// Single insert
const [newPost] = await db
  .insert(posts)
  .values({
    title: "Hello",
    content: "World",
    authorId: userId,
  })
  .returning()

// Batch insert
await db.insert(posts).values([
  { title: "Post 1", content: "...", authorId: userId },
  { title: "Post 2", content: "...", authorId: userId },
])
```

### Update

```typescript
const [updated] = await db
  .update(posts)
  .set({ title: "New Title", updatedAt: new Date() })
  .where(eq(posts.id, postId))
  .returning()
```

### Delete

```typescript
await db.delete(posts).where(eq(posts.id, postId))
```

## Migration Commands

```bash
pnpm db:generate    # Generate migration from schema changes
pnpm db:push        # Push schema directly (development)
pnpm db:migrate     # Run pending migrations (production)
pnpm db:studio      # Open Drizzle Studio GUI
```

## Drizzle Config

File: `drizzle.config.ts`

```typescript
import { defineConfig } from "drizzle-kit"

export default defineConfig({
  schema: "./src/db/schema/*",
  out: "./src/db/migrations",
  dialect: "postgresql",
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
})
```

## Anti-Patterns

- Never write raw SQL - use Drizzle query builder
- Never skip `.returning()` on insert/update when you need the result
- Never define relations in schema files - use separate relations.ts
- Never use `db.execute()` for standard CRUD - use typed queries
- Never forget to export inferred types (`$inferSelect`, `$inferInsert`)
- Never put DB client creation in component scope - keep it in `src/db/index.ts`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enesdmc0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
