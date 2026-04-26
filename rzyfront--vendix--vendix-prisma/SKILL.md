---
name: vendix-prisma
description: > Use when this capability is needed.
metadata:
  author: rzyfront
---

## When to Use

Use this skill when:

- Modifying Prisma schema
- Creating or running migrations
- Using Prisma Client in services
- Seeding database

## Critical Patterns

### Pattern 1: Schema Structure

**apps/backend/prisma/schema.prisma**:

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  orders    Order[]
}

model Order {
  id        String   @id @default(uuid())
  userId    String
  user      User     @relation(fields: [userId], references: [id])
  status    String   @default("PENDING")
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  items     OrderItem[]
}

model OrderItem {
  id        String   @id @default(uuid())
  orderId   String
  order     Order    @relation(fields: [orderId], references: [id])
  productId String
  quantity  Int
  price     Decimal  @db.Decimal(10, 2)
}
```

### Pattern 2: Prisma Service Architecture

Vendix uses **domain-scoped Prisma services** built on an abstract `BasePrismaService`. Each domain injects the appropriate scoped service:

| Service                     | Scope                  | Use Case                    |
| --------------------------- | ---------------------- | --------------------------- |
| `GlobalPrismaService`       | None                   | Superadmin, background jobs |
| `OrganizationPrismaService` | `organization_id`      | Org admin                   |
| `StorePrismaService`        | `store_id`             | Store admin, POS            |
| `EcommercePrismaService`    | `store_id` + `user_id` | Customer e-commerce         |

> **For complete scoping documentation, model registration rules, and `withoutScope()` guidelines, see `vendix-prisma-scopes` skill.**

### Pattern 3: Using Prisma in Services

```typescript
import { Injectable } from "@nestjs/common";
import { StorePrismaService } from "@/prisma/services/store-prisma.service";

@Injectable()
export class UsersService {
  constructor(private readonly prisma: StorePrismaService) {}

  async findAll() {
    // store_id auto-applied by scope
    return this.prisma.store_users.findMany();
  }

  async findOne(id: number) {
    return this.prisma.store_users.findUnique({
      where: { id },
    });
  }

  async create(data: { user_name: string; email: string }) {
    // store_id auto-injected on create
    return this.prisma.store_users.create({ data });
  }

  async update(id: number, data: { user_name?: string }) {
    return this.prisma.store_users.update({
      where: { id },
      data,
    });
  }

  async remove(id: number) {
    return this.prisma.store_users.delete({
      where: { id },
    });
  }
}
```

### Pattern 4: Transaction Handling

```typescript
async createOrderWithItems(orderData: CreateOrderDto) {
  return this.prisma.$transaction(async (tx) => {
    const order = await tx.order.create({
      data: {
        userId: orderData.userId,
        status: 'PENDING',
      },
    });

    await tx.orderItem.createMany({
      data: orderData.items.map((item) => ({
        orderId: order.id,
        productId: item.productId,
        quantity: item.quantity,
        price: item.price,
      })),
    });

    return order;
  });
}
```

### Pattern 5: Complex Queries with Relations

```typescript
// Find orders with user and items
async getOrdersWithDetails() {
  return this.prisma.order.findMany({
    include: {
      user: true,
      items: {
        include: {
          product: true,
        },
      },
    },
  });
}

// Find with filtering
async getOrdersByStatus(status: string) {
  return this.prisma.order.findMany({
    where: { status },
    include: { items: true },
  });
}

// Find with pagination
async getOrdersPaginated(page: number, limit: number) {
  const skip = (page - 1) * limit;
  const [orders, total] = await Promise.all([
    this.prisma.order.findMany({
      skip,
      take: limit,
      include: { user: true },
    }),
    this.prisma.order.count(),
  ]);

  return { orders, total, page, limit };
}
```

## Decision Tree

```
Modifying database schema?
├── Edit schema.prisma
├── Run migration: npx prisma migrate dev
├── Review generated SQL
└── Regenerate Prisma Client

Adding new model?
├── Add model to schema.prisma
├── Define relations with @relation
├── Create migration
└── Update services to use new model

Seeding database?
├── Create seed file in prisma/seed.ts
├── Add to package.json: "prisma": { "seed": "ts-node ..." }
├── Run: npx prisma migrate reset
└── Data seeded automatically

Debugging Prisma?
├── Enable query logging in PrismaService
├── Use Prisma Studio: npx prisma studio
└── Check DATABASE_URL in .env
```

## Code Examples

### Example 1: Creating Migration

```bash
# After editing schema.prisma
cd apps/backend
npx prisma migrate dev --name add_user_roles

# This creates:
# - prisma/migrations/TIMESTAMP_add_user_roles/migration.sql
# - Regenerates Prisma Client
```

### Example 2: Seeding Database

**prisma/seed.ts**:

```typescript
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

async function main() {
  await prisma.user.createMany({
    data: [
      { email: "admin@vendix.com", name: "Admin" },
      { email: "user@vendix.com", name: "User" },
    ],
  });
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

**package.json**:

```json
{
  "prisma": {
    "seed": "ts-node prisma/seed.ts"
  }
}
```

### Example 3: Error Handling

```typescript
async updateWithConflictHandling(id: string, email: string) {
  try {
    return await this.prisma.user.update({
      where: { id },
      data: { email },
    });
  } catch (error) {
    if (error.code === 'P2002') {
      // Unique constraint violation
      throw new ConflictException('Email already exists');
    }
    if (error.code === 'P2025') {
      // Record not found
      throw new NotFoundException('User not found');
    }
    throw error;
  }
}
```

## Commands

```bash
# Create migration after schema change
cd apps/backend
npx prisma migrate dev --name describe_change

# Reset database (WARNING: deletes all data)
npx prisma migrate reset

# Deploy migrations in production
npx prisma migrate deploy

# Open Prisma Studio (GUI)
npx prisma studio

# Seed database
npx prisma db seed

# Format Prisma schema
npx prisma format

# Validate Prisma schema
npx prisma validate

# Generate Prisma Client (if needed manually)
npx prisma generate
```

## Environment Variables

**apps/backend/.env**:

```env
DATABASE_URL="postgresql://postgres:password@localhost:5432/vendix_db?schema=public"
```

**Docker DATABASE_URL** (for containers):

```env
DATABASE_URL="postgresql://postgres:password@db:5432/vendix_db?schema=public"
```

## Resources

- **Prisma Schema**: [apps/backend/prisma/schema.prisma](../../../apps/backend/prisma/schema.prisma)
- **Prisma Scopes**: See [skills/vendix-prisma-scopes/SKILL.md](../vendix-prisma-scopes/SKILL.md) for scoping system
- **Prisma Docs**: https://www.prisma.io/docs
- **Reference**: See [skills/vendix-backend/SKILL.md](../vendix-backend/SKILL.md) for NestJS integration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rzyfront) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
