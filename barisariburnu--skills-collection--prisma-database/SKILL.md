---
name: prisma-database
description: Complete Prisma ORM guide with SQLite for schema design, migrations, queries, relations, and database operations. Use when working with databases, defining models, creating CRUD operations, or optimizing queries. Includes token-efficient patterns for common database tasks. Use when this capability is needed.
metadata:
  author: barisariburnu
---

# Prisma Database Skill

**Skill Location**: `{project_path}/skills/prisma-database/`

Comprehensive guide for Prisma ORM with SQLite, optimized for minimal token usage while maintaining production-quality database operations.

---

## When to Use This Skill (Trigger Patterns)

**MUST apply this skill when:**
- Defining database schemas and models
- Creating CRUD operations
- Working with relations and foreign keys
- Writing complex queries
- Optimizing database performance
- Handling transactions
- Seeding or migrating data

**Trigger phrases:**
- "create a database model"
- "add a new table/collection"
- "write database queries"
- "add relations between models"
- "optimize database performance"
- "seed database"

---

## Project Database Setup

### Configuration Files

**prisma/schema.prisma** - Schema definition
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

// Models here
```

**db/custom.db** - SQLite database file (auto-created)
**src/lib/db.ts** - Prisma client singleton

---

## Token-Saving Strategies

### 1. Reference Common Patterns, Don't Explain Basics

**❌ INEFFICIENT:**
```
"Prisma is an ORM that helps you interact with databases..."
```

**✅ EFFICIENT:**
```
// Use standard Prisma patterns
// Import from @/lib/db
```

### 2. Use Minimal Comments

**❌ INEFFICIENT:**
```prisma
// This model represents a user in our system
// It has an id that is unique
model User {
  id String @id @default(cuid())
}
```

**✅ EFFICIENT:**
```prisma
model User {
  id String @id @default(cuid())
}
```

### 3. Assume Prisma Knowledge

Don't explain:
- What `@id`, `@default`, `@unique` do
- How to use `findMany`, `findFirst`, `findUnique`
- Basic relation concepts

Focus on:
- Schema design decisions
- Query optimization
- Advanced patterns

---

## Schema Definition Patterns

### 1. Basic Model Template

```prisma
model ModelName {
  id        String   @id @default(cuid())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  // Your fields here
}
```

### 2. Common Field Types

```prisma
model Example {
  // ID
  id String @id @default(cuid())

  // Numbers (SQLite uses Int)
  count    Int
  price    Float
  quantity Int

  // Strings
  name     String
  email    String?
  content  String   @db.Text

  // Boolean (SQLite uses Int)
  isActive Boolean
  isDeleted Boolean @default(false)

  // Dates
  birthDate DateTime?
  expiresAt DateTime?

  // Enums
  status    Status   @default(ACTIVE)

  // Timestamps
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

enum Status {
  ACTIVE
  INACTIVE
  PENDING
  DELETED
}
```

### 3. One-to-Many Relations

```prisma
model User {
  id      String  @id @default(cuid())
  name    String
  email   String  @unique
  posts   Post[]
  comments Comment[]
}

model Post {
  id        String   @id @default(cuid())
  title     String
  content   String
  authorId  String
  author    User     @relation(fields: [authorId], references: [id])
  comments  Comment[]
}

model Comment {
  id      String @id @default(cuid())
  content String
  postId  String
  userId  String
  post    Post   @relation(fields: [postId], references: [id])
  user    User   @relation(fields: [userId], references: [id])
}
```

### 4. Many-to-Many Relations

```prisma
model User {
  id    String   @id @default(cuid())
  name  String
  posts Post[]   @relation("UserPosts")
}

model Post {
  id      String  @id @default(cuid())
  title   String
  users   User[]  @relation("UserPosts")
  tags    Tag[]
}

model Tag {
  id    String @id @default(cuid())
  name  String  @unique
  posts Post[]
}
```

### 5. Self-Referencing Relations

```prisma
model Comment {
  id         String    @id @default(cuid())
  content    String
  parentId   String?
  parent     Comment?  @relation("CommentReplies", fields: [parentId], references: [id])
  replies    Comment[] @relation("CommentReplies")
}
```

---

## Query Patterns

### 1. Import Prisma Client

```typescript
import { db } from '@/lib/db'
```

### 2. CRUD Operations

```typescript
// Create
const user = await db.user.create({
  data: {
    name: 'John Doe',
    email: 'john@example.com'
  }
})

// Read - Unique
const user = await db.user.findUnique({
  where: { id: userId }
})

// Read - First
const user = await db.user.findFirst({
  where: { email: 'john@example.com' }
})

// Read - Many
const users = await db.user.findMany({
  where: { isActive: true }
})

// Update
const user = await db.user.update({
  where: { id: userId },
  data: { name: 'Jane Doe' }
})

// Delete
await db.user.delete({
  where: { id: userId }
})

// Upsert
const user = await db.user.upsert({
  where: { email: 'john@example.com' },
  update: { name: 'Jane Doe' },
  create: { name: 'John Doe', email: 'john@example.com' }
})
```

### 3. Advanced Queries

```typescript
// Where conditions
const users = await db.user.findMany({
  where: {
    AND: [
      { isActive: true },
      { name: { contains: 'John' } }
    ],
    OR: [
      { email: { endsWith: '@company.com' } },
      { role: 'ADMIN' }
    ]
  }
})

// Pagination
const users = await db.user.findMany({
  skip: (page - 1) * limit,
  take: limit,
  orderBy: { createdAt: 'desc' }
})

// With relations
const posts = await db.post.findMany({
  include: {
    author: {
      select: { id: true, name: true, email: true }
    },
    comments: {
      where: { isDeleted: false },
      take: 10
    }
  }
})

// Aggregations
const stats = await db.user.aggregate({
  _count: { id: true },
  _avg: { age: true },
  _sum: { points: true },
  where: { isActive: true }
})

// Transaction
await db.$transaction([
  db.user.create({
    data: { name: 'John', email: 'john@example.com' }
  }),
  db.post.create({
    data: {
      title: 'First Post',
      authorId: 'user-id'
    }
  })
])
```

### 4. Batch Operations

```typescript
// Create many
await db.user.createMany({
  data: [
    { name: 'John', email: 'john@example.com' },
    { name: 'Jane', email: 'jane@example.com' }
  ],
  skipDuplicates: true
})

// Update many
await db.user.updateMany({
  where: { isActive: false },
  data: { isDeleted: true }
})

// Delete many
await db.user.deleteMany({
  where: { isDeleted: true }
})
```

---

## Database Operations Workflow

### 1. Adding a New Model

**Steps:**
1. Add model to `prisma/schema.prisma`
2. Run `bun run db:push`
3. Import db client: `import { db } from '@/lib/db'`
4. Use in code

**Token-Saving Prompt:**
```
Add Product model:
- Fields: id, name, price, description, stock, status
- Relations: belongs to Category
- Use standard Prisma patterns
```

### 2. Adding Relations

**Prompt:**
```
Add relation between User and Order:
- User has many orders
- Order belongs to user
- Include foreign key constraints
```

### 3. Creating CRUD API

```typescript
// GET /api/users
export async function GET() {
  const users = await db.user.findMany()
  return NextResponse.json(users)
}

// POST /api/users
export async function POST(req: NextRequest) {
  const data = await req.json()
  const user = await db.user.create({ data })
  return NextResponse.json(user)
}

// GET /api/users/[id]
export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  const user = await db.user.findUnique({
    where: { id: params.id }
  })
  return NextResponse.json(user)
}

// PUT /api/users/[id]
export async function PUT(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  const data = await req.json()
  const user = await db.user.update({
    where: { id: params.id },
    data
  })
  return NextResponse.json(user)
}

// DELETE /api/users/[id]
export async function DELETE(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  await db.user.delete({
    where: { id: params.id }
  })
  return NextResponse.json({ success: true })
}
```

---

## Common Patterns

### 1. Soft Delete

```prisma
model User {
  id        String   @id @default(cuid())
  name      String
  isDeleted Boolean  @default(false)
  deletedAt DateTime?
}
```

```typescript
// Soft delete
await db.user.update({
  where: { id },
  data: { isDeleted: true, deletedAt: new Date() }
})

// Query only active
const activeUsers = await db.user.findMany({
  where: { isDeleted: false }
})
```

### 2. Slug Field

```prisma
model Post {
  id   String @id @default(cuid())
  slug String @unique
  title String
}
```

```typescript
import slugify from 'slugify'

const slug = slugify(title, { lower: true })

await db.post.create({
  data: { title, slug }
})
```

### 3. Timestamps

```prisma
model Post {
  id        DateTime @id @default(now())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  publishedAt DateTime?
}
```

---

## Performance Optimization

### 1. Select Only Needed Fields

```typescript
// Bad: Selects all fields
const user = await db.user.findUnique({ where: { id } })

// Good: Selects only needed fields
const user = await db.user.findUnique({
  where: { id },
  select: { id: true, name: true, email: true }
})
```

### 2. Use Indexes

```prisma
model Post {
  id        String   @id @default(cuid())
  slug      String   @unique
  title     String
  published Boolean  @default(false)
  authorId  String

  @@index([published])
  @@index([authorId, published])
}
```

### 3. Optimize Relations

```typescript
// Bad: N+1 queries
const posts = await db.post.findMany()
for (const post of posts) {
  const author = await db.user.findUnique({
    where: { id: post.authorId }
  })
}

// Good: Include relations
const posts = await db.post.findMany({
  include: { author: true }
})
```

### 4. Use Transactions for Multiple Operations

```typescript
await db.$transaction([
  db.order.create({ data: orderData }),
  db.product.updateMany({
    where: { id: { in: productIds } },
    data: { stock: { decrement: 1 } }
  })
])
```

---

## Common Pitfalls & Solutions

### ❌ Problem: Forgetting Relations in Queries

**❌ Bad:**
```typescript
const post = await db.post.findUnique({ where: { id } })
console.log(post.authorId) // Only ID, not full author
```

**✅ Good:**
```typescript
const post = await db.post.findUnique({
  where: { id },
  include: { author: true }
})
console.log(post.author.name) // Full author object
```

### ❌ Problem: N+1 Query Problem

**Solution:** Use `include` or `select` for relations

### ❌ Problem: Not Using Transactions

**Solution:** Use `db.$transaction()` for multi-step operations

### ❌ Problem: Over-fetching Data

**Solution:** Use `select` to fetch only needed fields

---

## Token-Efficient Prompt Templates

### Add Model
```
Add <MODEL> model:
- Fields: <field list>
- Relations: <relations>
- Use standard Prisma patterns
```

### CRUD Operations
```
Create CRUD for <MODEL>:
- GET /api/<resource>
- POST /api/<resource>
- PUT /api/<resource>/[id]
- DELETE /api/<resource>/[id]
- Include validation
```

### Complex Query
```
Query <MODEL>:
- Conditions: <where conditions>
- Include: <relations>
- Sort by: <field>
- Pagination: <page/limit>
```

---

## Quick Commands

```bash
# Push schema to database
bun run db:push

# Open Prisma Studio
bun run db:studio

# Generate Prisma Client (auto-run on push)
bunx prisma generate
```

---

## Important Reminders

1. **Prisma schema** → `prisma/schema.prisma`
2. **Database client** → Import from `@/lib/db`
3. **After schema changes** → Run `bun run db:push`
4. **SQLite only** - No MySQL, Redis, or other databases
5. **Use transactions** for multi-step operations
6. **Select only needed fields** - Avoid over-fetching
7. **Use indexes** for frequently queried fields
8. **Soft delete** instead of hard delete when possible

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/barisariburnu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
