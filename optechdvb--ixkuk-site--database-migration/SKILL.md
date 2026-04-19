---
name: database-migration
description: Use when working with database schemas, Prisma models, creating migrations, or modifying database structure. Ensures safe database changes following Prisma and MariaDB best practices.
metadata:
  author: optechdvb
---

# Database Migration Skill

You are working on a project using **Prisma ORM** with **MariaDB**. This skill guides you when working with database schemas and migrations to ensure safe, well-designed changes.

## Core Principles

### 1. Always Read Schema First
Before ANY database work:
```bash
Read prisma/schema.prisma
```

Understand:
- Current models and their relationships
- Field types and constraints
- Existing enums
- Indexes and unique constraints

### 2. Understand the Database Stack
- **Database**: MariaDB (NOT standard MySQL - subtle differences)
- **ORM**: Prisma 7.2.0
- **Adapter**: `@prisma/adapter-mariadb` (connection pooling)
- **Client**: Singleton in `src/lib/prisma.ts`
- **Connection**: Via `DATABASE_URL` environment variable

### 3. Current Models
The project has these models:
- **Subscribers**: Newsletter subscribers with email, state (enum), locale
- **SubscriberVerifications**: Email verification codes with expiration
- **Contacts**: Contact form submissions with Rotary fields
- **SubState**: Enum (ACTIVE, UNSUBSCRIBED, BLOCKED)

### 4. Never Skip These Steps

**For ANY schema change**:
1. ✅ Read current schema
2. ✅ Discuss with user if unclear
3. ✅ Edit schema file
4. ✅ Create migration: `pnpm prisma:migrate:dev --name descriptive_name`
5. ✅ Generate client: `pnpm prisma:generate`
6. ✅ Check for TypeScript errors
7. ✅ Update code that uses the schema

## Safe Schema Changes

### Adding Fields (SAFE)
```prisma
model Subscribers {
  SubscriberID  Int      @id @default(autoincrement())
  email         String   @unique @db.VarChar(255)

  // SAFE: Optional field
  firstName     String?  @db.VarChar(100)

  // SAFE: Field with default
  isActive      Boolean  @default(true)
  joinedAt      DateTime @default(now())
}
```

### Adding Fields (RISKY - Warn User!)
```prisma
// ⚠️ RISKY: Required field with no default
// Will fail if table has existing data!
lastName  String  @db.VarChar(100)

// Better: Make it optional first, backfill data, then make required
lastName  String?  @db.VarChar(100)  // Add as optional
// Later, after backfilling data:
// lastName  String  @db.VarChar(100)  // Make required
```

### Removing Fields (WARN USER - Data Loss!)
```prisma
// ⚠️ WARNING: Removing field = DATA LOSS
// Always confirm with user before removing fields
// model Subscribers {
//   oldField  String?  // Removing this - data will be lost!
// }
```

### Changing Field Types (RISKY - Warn User!)
```prisma
// ⚠️ RISKY: Type changes might fail with existing data
// Old: email  String  @db.VarChar(100)
// New: email  String  @db.VarChar(255)  // Expanding is usually safe

// Old: age  String  @db.VarChar(10)
// New: age  Int  // Converting String to Int - might fail!
```

## Common Field Types (MariaDB)

```prisma
// Strings
name          String   @db.VarChar(255)  // Variable length
bio           String   @db.Text          // Long text
slug          String   @db.Char(10)      // Fixed length

// Numbers
id            Int                        // Integer
views         BigInt                     // Large integer
price         Decimal  @db.Decimal(10,2) // Precise (money)
rating        Float                      // Approximate

// Dates
createdAt     DateTime @default(now())
updatedAt     DateTime @updatedAt
birthDate     DateTime @db.Date          // Date only
meetingTime   DateTime @db.Time          // Time only

// Boolean
isActive      Boolean  @default(true)    // TINYINT(1)

// JSON
metadata      Json

// Optional
middleName    String?  @db.VarChar(100)  // Nullable
```

## Migration Naming

Use descriptive, snake_case names:

**Good names**:
- `add_subscriber_first_name`
- `create_donations_table`
- `add_index_on_email`
- `modify_contacts_phone_format`
- `remove_deprecated_status_field`

**Bad names**:
- `migration1`
- `update`
- `changes`
- `fix`

## Common Patterns

### Pattern 1: Add Optional Field
```prisma
model Subscribers {
  // ... existing fields

  // Add new optional field:
  preferredName  String?  @db.VarChar(100)
}
```

**Command**:
```bash
pnpm prisma:migrate:dev --name add_subscriber_preferred_name
```

### Pattern 2: Add Field with Default
```prisma
model Contacts {
  // ... existing fields

  // Add field with default:
  status  String  @default("new") @db.VarChar(50)
}
```

**Command**:
```bash
pnpm prisma:migrate:dev --name add_contact_status_field
```

### Pattern 3: Add Enum
```prisma
enum DonationStatus {
  PENDING
  COMPLETED
  FAILED
  REFUNDED
}

model Donation {
  id      Int             @id @default(autoincrement())
  status  DonationStatus  @default(PENDING)
  amount  Decimal         @db.Decimal(10, 2)
}
```

### Pattern 4: Add Relationship
```prisma
model Subscribers {
  SubscriberID  Int         @id @default(autoincrement())
  email         String      @unique @db.VarChar(255)

  // Add relationship:
  donations     Donation[]
}

model Donation {
  id            Int         @id @default(autoincrement())
  subscriberId  Int
  subscriber    Subscribers @relation(fields: [subscriberId], references: [SubscriberID], onDelete: Cascade)
  amount        Decimal     @db.Decimal(10, 2)
}
```

### Pattern 5: Add Index
```prisma
model Subscribers {
  SubscriberID  Int      @id @default(autoincrement())
  email         String   @unique @db.VarChar(255)
  locale        String   @db.VarChar(10)
  createdAt     DateTime @default(now())

  // Add indexes for frequently queried fields:
  @@index([locale])
  @@index([createdAt])
}
```

## Workflow

### Simple Change (e.g., Add Optional Field)
```bash
1. Read prisma/schema.prisma
2. Edit schema (add field)
3. pnpm prisma:migrate:dev --name add_field_name
4. pnpm prisma:generate
5. Update code to use new field
6. Test
```

### Complex Change (Delegate to Subagent)
For complex changes, consider delegating:
- Creating multiple related models
- Major schema refactoring
- Complex relationship changes
- Performance optimization with indexes

**Delegate**: "Use the db-migration subagent to create a complete events system with registrations"

## Important Checks

### Before Creating Migration
- [ ] Read current schema
- [ ] New field is optional OR has default (if table has data)
- [ ] Relationships are correct (fields, references, onDelete)
- [ ] Field types are appropriate for MariaDB
- [ ] Migration name is descriptive

### After Creating Migration
- [ ] Migration created in `prisma/migrations/`
- [ ] Ran `pnpm prisma:generate`
- [ ] No TypeScript errors
- [ ] Updated code to use new schema
- [ ] Tested CRUD operations

### Before Removing Anything
- [ ] **Confirmed with user** - data will be lost!
- [ ] Considered archiving/soft-delete instead
- [ ] Backed up data if needed

## When to Delegate to Subagent

Use the **db-migration subagent** for:
- Creating new complex models with multiple relationships
- Major schema refactoring
- Adding multiple indexes for performance
- Complex migration scenarios
- When you need deep database expertise

Use **this skill** (me directly) for:
- Adding single fields
- Simple type changes
- Creating basic migrations
- Straightforward schema updates

## Working with Prisma Client

After schema changes, the Prisma client has updated types:

```typescript
import { prisma } from '@/lib/prisma';

// Query with new fields:
const subscriber = await prisma.subscribers.findUnique({
  where: { email: 'user@example.com' },
  select: {
    SubscriberID: true,
    email: true,
    // New field available:
    firstName: true,
  }
});

// Create with new fields:
await prisma.subscribers.create({
  data: {
    email: 'new@example.com',
    firstName: 'John',  // New field
  }
});
```

## Commands Reference

```bash
# Development: Create and apply migration
pnpm prisma:migrate:dev --name migration_name

# Generate Prisma Client (after schema changes)
pnpm prisma:generate

# Production: Deploy migrations only
pnpm prisma:migrate:deploy

# Check migration status
pnpm prisma migrate status

# Open Prisma Studio (GUI for data)
pnpm prisma:studio

# Reset database (⚠️ DEVELOPMENT ONLY - destroys data!)
pnpm prisma migrate reset
```

## Error Handling

### "Migration failed to apply"
- Check schema syntax
- Verify field types are valid
- Check for constraint violations
- Read error message carefully

### "Required field without default"
- Make field optional: `field String?`
- Or add default: `field String @default("")`

### "Relation field missing"
- Ensure both sides of relationship defined
- Check field and reference names match
- Verify onDelete behavior

## Important Reminders

1. **MariaDB, not MySQL** - Subtle differences exist
2. **Read schema first** - Always understand current state
3. **Name migrations well** - Future you will thank you
4. **Optional is safer** - For existing data
5. **Warn on data loss** - Before removing/changing fields
6. **Generate after migrate** - Don't forget this step!
7. **Check TypeScript** - Ensure no type errors
8. **Test thoroughly** - CRUD operations with new schema

## Quality Checklist

Before marking database work complete:

- [ ] Read current schema before making changes
- [ ] Schema changes are backward compatible (or user warned)
- [ ] Migration name is descriptive and follows convention
- [ ] Migration created successfully
- [ ] Prisma Client regenerated
- [ ] No TypeScript errors in codebase
- [ ] Updated code to use new schema
- [ ] Tested create/read/update operations
- [ ] Documented significant changes in CLAUDE.md (if major)

## Resources

- **Schema file**: `prisma/schema.prisma`
- **Client singleton**: `src/lib/prisma.ts`
- **Migrations**: `prisma/migrations/`
- **Documentation**: [Prisma with MariaDB](https://www.prisma.io/docs/orm/overview/databases/mariadb)
- **For complex work**: Delegate to `db-migration` subagent

Remember: Database changes are permanent. Take time to get them right!

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/optechdvb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
