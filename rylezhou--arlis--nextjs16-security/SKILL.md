---
name: next-js-16-security-standards
description: Comprehensive security guidelines for Next.js 16+, covering Database (RLS), API, Server Actions, and Data Access patterns. Use when this capability is needed.
metadata:
  author: rylezhou
---

# Next.js 16 Security Standards

> **Version**: Next.js 16.1.6+
> **Last Updated**: 2026-02-04

This skill defines the **mandatory** security practices for developing within the Arlis codebase.

---

## Core Principle
**"Trust Nothing, Verify Everything."** 
User inputs, IDs, and even internal service calls must be validated. We assume the network is hostile and inputs are malicious until proven otherwise.

---

## 1. Data Access & Architecture

### A. Data Access Layer (DAL)
For all new features, use a **Data Access Layer**. This centralizes data fetching, authorization, and DTO creation.

- **Only run on the server.**
- **Perform authorization checks.**
- **Return safe, minimal Data Transfer Objects (DTOs).**

```ts filename="data/user-dto.tsx"
import 'server-only'
import { cache } from 'react'

export const getUserProfile = cache(async (slug: string) => {
  // 1. Fetch Data
  const user = await db.query(...) 

  // 2. Authz Check
  const currentUser = await getCurrentUser()
  if (!canViewProfile(currentUser, user)) throw new Error("Unauthorized")

  // 3. Return DTO (Sanitized)
  return {
    username: user.username,
    // EXCLUDE private fields like email, phone, etc. unless explicitly authorized
  }
})
```

### B. Prevention of Data Leakage
- **DTOs**: Always assume objects passed to Client Components *can* be inspected by the user. Filter them on the server first.
- **Tainting**: Use `experimental_taintObjectReference` to mark sensitive objects that should *never* reach the client.
- **Server-Only**: Mark internal libraries with `import 'server-only'` to prevent accidental client-side bundling.

---

## 2. Database Security (Supabase Integration)

### A. Row Level Security (RLS) is King
- **Rule**: Every single table must have RLS enabled.
- **Rule**: Never disable RLS for "convenience".
- **Pattern**: Use the standard `createClient()` for all user interactions.

### B. Service Role & IDOR Prevention
**DANGER**: `createAdminClient()` (Service Role) bypasses RLS.

- **When to use**: Background jobs, Webhooks, System-level tools.
- **Checklist**: If used in a user-facing route, you **MUST** manually verify ownership.

#### ✅ Secure Pattern (Admin Client)
```typescript
const { data: app } = await admin.from('apps').select('*').eq('id', appId).single();
const user = await getAuthenticatedUser();

// MANUAL OWNERSHIP CHECK
if (app.user_id !== user.id) {
  throw new Error("Unauthorized access attempt");
}
```

---

## 3. Server Actions & Mutations

Server Actions are public HTTP endpoints. Treat them with the same security rigor as REST APIs.

### A. Authentication & Authorization
Every Action must verify the user's identity and permission *inside the action*.

```typescript
'use server'

import { auth } from '@/lib/auth'

export async function updateProfile(data: FormData) {
  const user = await auth();
  if (!user) throw new Error('Unauthorized');
  
  // Custom Logic...
}
```

### B. Input Validation (Zod)
Never trust input, including FormData or arguments. Use Zod schema validation.

```typescript
import { z } from 'zod';

const Schema = z.object({
  email: z.string().email(),
  role: z.enum(['user', 'admin'])
});

export async function setupUser(data: unknown) {
    const parsed = Schema.parse(data); // Throws if invalid
    // ...
}
```

### C. Closures & Encryption
Server Actions inside components capture variables via **closures**. Next.js encrypts these, but **do not** rely on this for secrets.
- **Risk**: Encrypted data is sent to the client.
- **Mitigation**: Do not close over sensitive data (e.g., API keys, full user objects) if possible. Pass IDs and re-fetch on the server.

### D. CSRF & Origins
Next.js protects Server Actions via Origin checks.
- If using a proxy/load balancer, configure `serverActions.allowedOrigins` in `next.config.js`.

### E. No Side-Effects in Render
**Never** trigger mutations (DB writes, cookie setting) directly in a Component's render body. Only do this in Server Actions or Route Handlers.

---

## 4. Auditing Checklist

When reviewing code, verify:
- [ ] **DAL**: Are Database packages imported outside the Data Access Layer?
- [ ] **Client Props**: Are we passing full database objects to Client Components? (Check DTOs).
- [ ] **Server Actions**: Is there an `auth()` check at the top? Is input validated?
- [ ] **Routes**: Are `params` (e.g., `[id]`) validated before use?
- [ ] **RLS**: Is `createAdminClient()` used? If so, is there a manual auth check?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rylezhou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
