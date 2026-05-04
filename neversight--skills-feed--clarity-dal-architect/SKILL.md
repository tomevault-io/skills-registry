---
name: clarity-dal-architect
description: Enforce Clarity project-specific architecture patterns - DAL for auth, type centralization in @/lib/types, repository pattern, balance helpers, and Drizzle ORM usage. Project-specific compliance checker. Use when this capability is needed.
metadata:
  author: neversight
---

# Clarity DAL Architect

## Overview

Specialized skill for **Clarity project-specific** architecture patterns. Enforces:
- **DAL (Data Access Layer)** - Centralized auth with `getUserId()`, `verifySession()`
- **Type Centralization** - Domain types only in `@/lib/types`
- **Repository Pattern** - Data access in `/lib/db/repositories`
- **Balance Helpers** - Encryption-aware balance handling
- **Drizzle Imports** - Operators from `drizzle-orm`, not `@/lib/db`

## When to Use

Invoke when:
- "Audit Clarity architecture"
- "Check DAL usage"
- "Fix auth patterns"
- "Verify type imports"
- "Review database access"
- Working on Clarity-specific code

## Core Patterns

### 1. DAL for Authentication

```ts
// ✅ CORRECT - Server Components use DAL
import { getUserId, getUser, verifySession } from '@/lib/data/dal'

// Simple auth check (auto-redirects to /sign-in)
export default async function Page() {
  await verifySession()
  return <Component />
}

// Need user ID for queries
export default async function Page() {
  const userId = await getUserId() // Auto-redirects if unauthenticated
  const data = await fetchData(userId)
  return <Component data={data} />
}

// Need full User object
export default async function Page() {
  const user = await getUser() // Auto-redirects if unauthenticated
  return <Profile user={user} />
}

// Optional auth (don't redirect)
export default async function Page() {
  const session = await getSession() // Returns null if unauthenticated
  return session ? <UserContent /> : <PublicContent />
}

// ❌ WRONG - Direct Supabase auth
import { createClient } from '@/lib/db/supabase/server'

export default async function BadPage() {
  const supabase = await createClient()
  const { data: { user } } = await supabase.auth.getUser() // Don't do this!
  if (!user) redirect('/sign-in')
}
```

### 2. Type Centralization

```ts
// ✅ CORRECT - Import from @/lib/types
import type {
  Transaction,
  Account,
  User,
  Connection,
  Asset,
  Portfolio,
  Holding,
} from '@/lib/types'

export function TransactionList({ transactions }: { transactions: Transaction[] }) {
  return <ul>...</ul>
}

// ❌ WRONG - Local type definition
export function BadTransactionList({
  transactions,
}: {
  transactions: Array<{
    id: string
    amount: number
    date: string
  }>
}) {
  // Duplicating Transaction type!
}

// ❌ WRONG - Importing from wrong location
import type { Transaction } from '@/lib/transactions/types'
// Should be: import type { Transaction } from '@/lib/types'
```

### 3. Repository Pattern

```ts
// ✅ CORRECT - Use repositories
import * as AccountRepository from '@/lib/db/repositories/accounts'

export async function getAccountData(userId: string) {
  const accounts = await AccountRepository.findByUserId(userId)
  return accounts
}

// ❌ DEPRECATED - Direct queries module
import { accountQueries } from '@/lib/db/queries'
const accounts = await accountQueries.getByUserId(userId)
```

### 4. Balance Helpers

```ts
// ✅ CORRECT - Use getAccountBalance helper
import { getAccountBalance } from '@/lib/db/utils/balance-helpers'

export async function processAccount(account: Account) {
  const balance = getAccountBalance(
    account.balance,
    account.metadata,
    encryptionKey,
    account.id
  )
  // Helper has fallback to metadata if decryption fails
  return balance
}

// ❌ WRONG - Direct decryption (no fallback)
import { decryptBalance } from '@/lib/encryption'

export async function badProcessAccount(account: Account) {
  const balance = decryptBalance(account.balance, { key }) // Throws on failure!
  return balance
}
```

### 5. Drizzle ORM Imports

```ts
// ✅ CORRECT - Import operators from drizzle-orm
import { eq, and, or, gt, lt, isNull } from 'drizzle-orm'
import { db } from '@/lib/db'
import { accounts } from '@/lib/db/schema'

const userAccounts = await db.query.accounts.findMany({
  where: and(
    eq(accounts.userId, userId),
    gt(accounts.balance, 0)
  ),
})

// ❌ WRONG - Importing from @/lib/db
import { eq, and } from '@/lib/db'
// Should be: import { eq, and } from 'drizzle-orm'
```

### 6. Barrel Exports

```ts
// ✅ CORRECT - Import from index.ts
import { formatCurrency, formatDate } from '@/lib/utils'
import { getUserAccounts } from '@/lib/services/accounts'

// ❌ LESS IDEAL - Direct file imports (but acceptable)
import { formatCurrency } from '@/lib/utils/formatters/format'
import { getUserAccounts } from '@/lib/services/accounts/get-accounts'
```

## Client-Side Auth

### 1. useAuthUser Hook (Recommended)

```tsx
// ✅ CORRECT - Performance-optimized auth hook
'use client'

import { useAuthUser } from '@/hooks/use-auth-user'

export function ProfileSettings() {
  const { user, ready } = useAuthUser()

  if (!ready) return <div>Loading...</div>
  if (!user) return <div>Please sign in</div>

  return <Settings user={user} />
}
```

### 2. useAuth Context (Alternative)

```tsx
// ✅ ALTERNATIVE - Context-based auth
'use client'

import { useAuth, useRequireAuth } from '@/lib/hooks/contexts/auth-context'

export function UserProfile() {
  const { user, loading, signOut } = useAuth()

  if (loading) return <div>Loading...</div>
  if (!user) return <div>Not authenticated</div>

  return (
    <div>
      <p>Welcome {user.email}</p>
      <button onClick={signOut}>Sign Out</button>
    </div>
  )
}

// Or require auth (redirects if not authenticated)
export function ProtectedComponent() {
  useRequireAuth()

  return <div>Protected content</div>
}
```

## Architecture Layers

### Clarity's 3-Layer Type Architecture

```
┌─────────────────────────────────────┐
│  Presentation Layer                 │
│  (components/, app/)                │
│  - UI-specific types                │
│  - FormData, display formats        │
└─────────────────────────────────────┘
           │ transforms
           ▼
┌─────────────────────────────────────┐
│  Business Logic Layer               │
│  (@/lib/types) ← CANONICAL          │
│  - Domain types (Transaction, etc)  │
│  - Business rules                   │
└─────────────────────────────────────┘
           │ maps to
           ▼
┌─────────────────────────────────────┐
│  Data Layer                         │
│  (@/lib/db/schema)                  │
│  - Database schema types            │
│  - ORM-specific types               │
└─────────────────────────────────────┘
```

### Type Import Rules

```ts
// ✅ Domain types from @/lib/types
import type { Transaction, Account } from '@/lib/types'

// ✅ Schema types from @/lib/db/schema
import type { transactions, accounts } from '@/lib/db/schema'

// ✅ UI-specific types local to component
type TransactionListProps = {
  transactions: Transaction[]
  onSelect: (id: string) => void
}

// ❌ WRONG - Domain type defined locally
type Transaction = { id: string; amount: number }
// Should import from @/lib/types
```

## Audit System

### How to Audit

**Invoke audit mode:**
```
Audit Clarity patterns in app/(auth)/dashboard/page.tsx
```

The skill checks:
1. **Auth**: Using DAL methods (not direct Supabase)
2. **Types**: Importing from `@/lib/types` (not local definitions)
3. **Data Access**: Using repositories (not direct queries)
4. **Balance Helpers**: Using `getAccountBalance` (not direct decryption)
5. **Drizzle Imports**: From `drizzle-orm` (not `@/lib/db`)

### Audit Report

```markdown
## Clarity Architecture Audit: app/(auth)/dashboard/page.tsx

**Compliance Score**: 72/100 (Acceptable ⭐⭐⭐)

### ✅ Compliant Patterns
- DAL usage for auth (getUserId)
- Repository pattern for data access
- Type imports from @/lib/types

### 🚨 Critical Violations

#### 1. Direct Supabase Auth (Line 15)
**Current**:
```ts
const supabase = await createClient()
const { data: { user } } = await supabase.auth.getUser()
```

**Fix**: Use DAL
```ts
import { getUserId } from '@/lib/data/dal'
const userId = await getUserId()
```

**Impact**: HIGH - Bypassing centralized auth layer

#### 2. Local Type Definition (Line 25)
**Current**:
```ts
interface Transaction {
  id: string
  amount: number
}
```

**Fix**: Import canonical type
```ts
import type { Transaction } from '@/lib/types'
```

**Impact**: HIGH - Type duplication, inconsistent with codebase

### ⚠️ High Priority Issues

#### 3. Wrong Drizzle Import (Line 40)
**Current**:
```ts
import { eq } from '@/lib/db'
```

**Fix**: Import from drizzle-orm
```ts
import { eq } from 'drizzle-orm'
```

**Impact**: MEDIUM - Using deprecated export path

### Score Breakdown
- Auth (DAL): 15/25 🚨
- Type Centralization: 15/25 🚨
- Data Access: 20/20 ✅
- Balance Helpers: 10/10 ✅
- Drizzle Imports: 12/20 ⚠️
```

### Scoring Rubric

**Critical Violations (25 points each)**:
- [ ] Using DAL for auth (not direct Supabase)
- [ ] Types from `@/lib/types` (not local definitions)
- [ ] Repository pattern (not direct queries)
- [ ] No business logic in Server Components

**High Priority (15 points each)**:
- [ ] Balance helpers usage
- [ ] Drizzle imports from `drizzle-orm`
- [ ] Barrel exports used
- [ ] Cache Components integration

**Medium Priority (5 points each)**:
- [ ] Consistent error handling
- [ ] Proper type transformations
- [ ] Service layer usage
- [ ] Supabase client selection

## Project-Specific Conventions

### File Organization

```
/Users/zach/Documents/clarity/
├── lib/
│   ├── types/           # ← CANONICAL domain types
│   ├── data/
│   │   └── dal.ts       # ← Auth functions
│   ├── db/
│   │   ├── repositories/  # ← Data access
│   │   ├── services/      # ← Business logic
│   │   ├── schema/        # ← Drizzle schema
│   │   └── utils/
│   │       └── balance-helpers.ts  # ← Balance encryption
│   ├── cache/
│   │   └── tags.ts      # ← Cache tag constants
│   └── utils/
│       ├── formatters/
│       └── kv.ts        # ← Vercel KV wrapper
└── app/
    ├── (auth)/          # ← Protected routes
    ├── (public)/        # ← Public routes
    └── actions.ts       # ← Server Actions
```

### Cache Tags

```ts
// lib/cache/tags.ts
export const UserTags = {
  accounts: (userId: string) => `user:${userId}:accounts`,
  transactions: (userId: string) => `user:${userId}:transactions`,
  dashboard: (userId: string) => `user:${userId}:dashboard`,
  connections: (userId: string) => `user:${userId}:connections`,
  settings: (userId: string) => `user:${userId}:settings`,
}

export const DataTags = {
  CATEGORIES: 'data:categories',
  INSTITUTIONS: 'data:institutions',
  BENCHMARKS: 'data:benchmarks',
}

export const ContentTags = {
  POSTS: 'content:posts',
  HELP: 'content:help',
  MARKETING: 'content:marketing',
}

// Usage
import { UserTags } from '@/lib/cache/tags'

export async function getUserAccounts(userId: string) {
  'use cache'
  cacheTag(UserTags.accounts(userId))
  return await db.query.accounts.findMany(...)
}
```

### Supabase Client Selection

```ts
// Client Components / Browser
import { createClient } from '@/lib/db/supabase/browser'

// Server Components / API Routes
import { createClient } from '@/lib/db/supabase/server'

// Middleware
import { createClient } from '@/lib/db/supabase/middleware'

// DAL (preferred for auth)
import { getUserId, getUser } from '@/lib/data/dal'
```

### Known Patterns

```ts
// ✅ High-sophistication patterns in Clarity
- DAL Pattern: verifySession(), getUserId(), getUser()
- Repository Pattern: findByUserId(), findById()
- Cache Components: 'use cache' with cacheLife()
- Service Layer: Business logic in /lib/db/services
- Barrel Exports: from '@/lib/utils'
```

## Migration Guides

### Migrating to DAL

```ts
// ❌ BEFORE - Direct Supabase
import { createClient } from '@/lib/db/supabase/server'

export default async function Page() {
  const supabase = await createClient()
  const { data: { user }, error } = await supabase.auth.getUser()

  if (error || !user) {
    redirect('/sign-in')
  }

  const data = await fetchData(user.id)
  return <Component data={data} />
}

// ✅ AFTER - Using DAL
import { getUserId } from '@/lib/data/dal'

export default async function Page() {
  const userId = await getUserId() // Auto-redirects if unauthenticated

  const data = await fetchData(userId)
  return <Component data={data} />
}
```

### Migrating to Type Centralization

```ts
// ❌ BEFORE - Local type
// components/TransactionList.tsx
interface Transaction {
  id: string
  amount: number
  date: string
}

export function TransactionList({ transactions }: { transactions: Transaction[] }) {
  return <ul>...</ul>
}

// ✅ AFTER - Canonical type
import type { Transaction } from '@/lib/types'

export function TransactionList({ transactions }: { transactions: Transaction[] }) {
  return <ul>...</ul>
}
```

### Migrating to Repositories

```ts
// ❌ BEFORE - Direct queries
import { db } from '@/lib/db'
import { accounts } from '@/lib/db/schema'
import { eq } from 'drizzle-orm'

export async function getAccounts(userId: string) {
  return await db.query.accounts.findMany({
    where: eq(accounts.userId, userId),
  })
}

// ✅ AFTER - Repository
import * as AccountRepository from '@/lib/db/repositories/accounts'

export async function getAccounts(userId: string) {
  return await AccountRepository.findByUserId(userId)
}
```

## Anti-Patterns Specific to Clarity

### ❌ Bypassing DAL

```ts
// Direct Supabase auth calls
const { data: { user } } = await supabase.auth.getUser()
```

### ❌ Type Duplication

```ts
// Defining domain types locally
interface Transaction { ... }
interface Account { ... }
```

### ❌ Direct Balance Decryption

```ts
// No fallback to metadata
const balance = decryptBalance(account.balance, key)
```

### ❌ Wrong Import Paths

```ts
// Importing Drizzle operators from wrong place
import { eq, and } from '@/lib/db'

// Importing types from wrong place
import type { Transaction } from '@/lib/transactions/types'
```

## Best Practices

### 1. Always Use DAL for Auth

```ts
// Server Components
import { getUserId } from '@/lib/data/dal'

// Client Components
import { useAuthUser } from '@/hooks/use-auth-user'
```

### 2. Import Types from @/lib/types

```ts
import type {
  Transaction,
  Account,
  User,
  Connection,
} from '@/lib/types'
```

### 3. Use Repositories for Data Access

```ts
import * as Repository from '@/lib/db/repositories/[name]'
```

### 4. Use Balance Helpers

```ts
import { getAccountBalance } from '@/lib/db/utils/balance-helpers'
```

### 5. Correct Drizzle Imports

```ts
import { eq, and, or } from 'drizzle-orm'
```

## Success Criteria

Clarity-compliant code has:

✅ **DAL Usage** - All auth via getUserId(), verifySession()
✅ **Type Centralization** - Domain types from @/lib/types
✅ **Repository Pattern** - Data access via repositories
✅ **Balance Helpers** - Encryption-aware balance handling
✅ **Correct Imports** - Drizzle from drizzle-orm
✅ **Cache Integration** - UserTags for cache invalidation
✅ **Supabase Clients** - Correct client for context
✅ **Service Layer** - Business logic in services
✅ **No Type Duplication** - Reuse canonical types

## Resources

- `/lib/README.md` - Full lib/ architecture guide
- `/lib/types/ARCHITECTURE.md` - Type system documentation
- `/.claude/DAL_MIGRATION_SUMMARY.md` - DAL migration guide
- `/docs/lib-auth.md` - Auth patterns documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
