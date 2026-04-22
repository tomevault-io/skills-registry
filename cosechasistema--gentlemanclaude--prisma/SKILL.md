---
name: prisma
description: Prisma ORM patterns, schema design, migrations, MySQL integration. Use when this capability is needed.
metadata:
  author: cosechasistema
---
---
name: prisma
description: >
  Prisma ORM patterns, schema design, migrations, MySQL integration.
  Trigger: When writing Prisma schemas, queries, migrations, or seed files.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "1.0"
---

## When to Use

- Designing or modifying Prisma schemas
- Writing queries (findMany, create, update, transactions)
- Running migrations
- Setting up Prisma with NestJS
- Seeding the database
- Handling Prisma errors

---

## Schema Design (MySQL)

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

model User {
  id        String    @id @default(cuid())
  email     String    @unique @db.VarChar(255)
  name      String    @db.VarChar(100)
  bio       String?   @db.Text
  role      Role      @default(USER)
  posts     Post[]
  profile   Profile?
  createdAt DateTime  @default(now()) @db.DateTime(0)
  updatedAt DateTime  @updatedAt @db.DateTime(0)
  deletedAt DateTime? @db.DateTime(0)

  @@index([email])
  @@index([deletedAt])
  @@map("users")
}

model Post {
  id         String     @id @default(cuid())
  title      String     @db.VarChar(255)
  content    String     @db.Text
  published  Boolean    @default(false)
  authorId   String
  author     User       @relation(fields: [authorId], references: [id], onDelete: Cascade)
  categories Category[]

  @@index([authorId])
  @@index([published])
  @@map("posts")
}

model Category {
  id    String @id @default(cuid())
  name  String @unique @db.VarChar(50)
  posts Post[]

  @@map("categories")
}

enum Role {
  USER
  ADMIN
  MODERATOR
}
```

### Schema Rules

- **ALWAYS** use `@db.*` native types for every field
- **ALWAYS** add `@@index` on foreign keys and frequently queried fields
- **ALWAYS** use `@@map("snake_case")` for table names
- Use `cuid()` or `uuid()` for IDs, not autoincrement (unless required)
- Use `@db.DateTime(0)` for MySQL datetime fields
- Use `@db.Decimal(10, 2)` for money fields
- Use `@db.Text` for long strings, `@db.VarChar(n)` for bounded strings

---

## Relations

```prisma
// One-to-One
model Profile {
  userId String @unique
  user   User   @relation(fields: [userId], references: [id], onDelete: Cascade)
}

// One-to-Many
model Post {
  authorId String
  author   User @relation(fields: [authorId], references: [id])
  @@index([authorId])
}

// Many-to-Many (implicit — Prisma creates join table)
model Post { categories Category[] }
model Category { posts Post[] }

// Many-to-Many (explicit — with metadata)
model CategoryOnPost {
  postId     String
  categoryId String
  assignedAt DateTime @default(now())
  post       Post     @relation(fields: [postId], references: [id])
  category   Category @relation(fields: [categoryId], references: [id])
  @@id([postId, categoryId])
}
```

---

## Query Patterns

### Select Only What You Need

```typescript
// ✅ select specific fields
const users = await prisma.user.findMany({
  select: { id: true, email: true, name: true },
})

// ✅ include relations with filters
const user = await prisma.user.findUnique({
  where: { id },
  include: {
    posts: { where: { published: true }, select: { id: true, title: true } },
  },
})
```

### Pagination

```typescript
// ✅ Cursor-based (scales for large datasets)
const posts = await prisma.post.findMany({
  take: 10,
  skip: 1,
  cursor: { id: lastPostId },
  orderBy: { id: 'asc' },
})

// Offset-based (simpler, doesn't scale)
const posts = await prisma.post.findMany({
  skip: (page - 1) * pageSize,
  take: pageSize,
})
```

**Decision: Large dataset or infinite scroll → cursor. Small dataset or page numbers → offset.**

---

## Transactions

```typescript
// Sequential (independent operations, single round trip)
const [user, post] = await prisma.$transaction([
  prisma.user.create({ data: userData }),
  prisma.post.delete({ where: { id: postId } }),
])

// Interactive (dependent operations with logic)
const result = await prisma.$transaction(async (tx) => {
  const account = await tx.account.findUnique({ where: { id: fromId } })
  if (account.balance < amount) throw new Error('Insufficient funds')

  await tx.account.update({ where: { id: fromId }, data: { balance: { decrement: amount } } })
  await tx.account.update({ where: { id: toId }, data: { balance: { increment: amount } } })
  return { success: true }
})
```

**CRITICAL: Keep interactive transactions SHORT. NEVER make HTTP calls inside them.**

---

## Error Handling

```typescript
import { Prisma } from '@prisma/client';

try {
  await prisma.user.create({ data });
} catch (error) {
  if (error instanceof Prisma.PrismaClientKnownRequestError) {
    switch (error.code) {
      case 'P2002': // Unique constraint violation
        throw new ConflictException('Resource already exists');
      case 'P2025': // Record not found
        throw new NotFoundException('Resource not found');
      case 'P2003': // Foreign key constraint
        throw new BadRequestException('Related resource not found');
    }
  }
  throw error;
}
```

---

## NestJS Integration

```typescript
// prisma.service.ts
@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit, OnModuleDestroy {
  async onModuleInit() { await this.$connect(); }
  async onModuleDestroy() { await this.$disconnect(); }
}

// prisma.module.ts
@Global()
@Module({
  providers: [PrismaService],
  exports: [PrismaService],
})
export class PrismaModule {}

// Usage in any service
@Injectable()
export class UsersService {
  constructor(private prisma: PrismaService) {}
  async findAll() { return this.prisma.user.findMany(); }
}
```

### Prisma Exception Filter (NestJS)

```typescript
@Catch(Prisma.PrismaClientKnownRequestError)
export class PrismaExceptionFilter extends BaseExceptionFilter {
  catch(exception: Prisma.PrismaClientKnownRequestError, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();

    const map: Record<string, number> = {
      P2002: HttpStatus.CONFLICT,
      P2025: HttpStatus.NOT_FOUND,
      P2003: HttpStatus.BAD_REQUEST,
    };

    const status = map[exception.code] ?? HttpStatus.INTERNAL_SERVER_ERROR;
    response.status(status).json({ statusCode: status, error: exception.code });
  }
}
```

---

## Seeding

```typescript
// prisma/seed.ts
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();

async function main() {
  await prisma.user.deleteMany();

  await prisma.user.create({
    data: {
      email: 'admin@example.com',
      name: 'Admin',
      role: 'ADMIN',
      posts: { create: [{ title: 'First Post', content: 'Hello', published: true }] },
    },
  });
}

main()
  .catch((e) => { console.error(e); process.exit(1); })
  .finally(() => prisma.$disconnect());
```

```json
// package.json
{ "prisma": { "seed": "tsx prisma/seed.ts" } }
```

---

## Soft Delete Pattern

```prisma
model User {
  deletedAt DateTime? @db.DateTime(0)
  @@index([deletedAt])
}
```

```typescript
// Use Prisma Client Extensions
const prisma = new PrismaClient().$extends({
  query: {
    $allModels: {
      async findMany({ args, query }) {
        args.where = { ...args.where, deletedAt: null };
        return query(args);
      },
      async delete({ args, query }) {
        return (query as any)({ ...args, data: { deletedAt: new Date() } });
      },
    },
  },
});
```

---

## Commands

```bash
npx prisma init                          # Initialize Prisma
npx prisma generate                      # Generate client
npx prisma migrate dev --name init       # Create + apply migration (dev)
npx prisma migrate deploy                # Apply migrations (production)
npx prisma migrate reset                 # Reset DB + reapply
npx prisma db push                       # Push schema (no migration files)
npx prisma db seed                       # Run seed
npx prisma studio                        # Visual DB browser
npx prisma format                        # Format schema
npx prisma validate                      # Validate schema
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cosechasistema) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
