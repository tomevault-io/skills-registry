---
name: convex-better-auth-dual-database
description: | Use when this capability is needed.
metadata:
  author: strataga
---

# Convex + Better Auth Dual-Database Architecture

## Problem
When using Better Auth with Convex, users exist in TWO separate locations:
1. **Better Auth component tables**: `betterAuth.user`, `betterAuth.account`, `betterAuth.session`
2. **App's users table**: Your custom `users` table in the main schema

This causes confusing errors like "User not found" when the user exists in one location but not the other.

## Context / Trigger Conditions
- "User not found" during login or password reset, but you see the user in your app's database
- User can't authenticate despite having a record in the `users` table
- Need to manually create an admin user in production
- Debugging auth flows where users should exist but operations fail
- Export shows users in `_components/betterAuth/user/documents.jsonl` separate from `users/documents.jsonl`

## Architecture

```
┌─────────────────────────────────────────┐
│           Better Auth Component          │
│  ┌─────────────┐  ┌──────────────────┐  │
│  │ user table  │  │ account table    │  │
│  │ - email     │  │ - password hash  │  │
│  │ - name      │  │ - providerId     │  │
│  │ - verified  │  │ - userId         │  │
│  └─────────────┘  └──────────────────┘  │
└─────────────────────────────────────────┘
                    │
                    │ Login/Auth happens here
                    ▼
┌─────────────────────────────────────────┐
│           App's users table              │
│  - email                                 │
│  - name                                  │
│  - isAdmin                               │
│  - subscriptionStatus                    │
│  - (business-specific fields)            │
└─────────────────────────────────────────┘
     Synced via syncFromAuth mutation
```

## Key Points

1. **Authentication uses Better Auth tables ONLY**
   - Login validates against `betterAuth.user` and `betterAuth.account`
   - Password hashes are stored in `betterAuth.account`
   - Sessions are in `betterAuth.session`

2. **App's users table is for business logic**
   - Stores app-specific fields (isAdmin, subscriptionStatus, etc.)
   - Must be synced AFTER Better Auth user is created
   - Sync happens via a mutation like `syncFromAuth`

3. **Component tables can't be directly accessed**
   - Can't write mutations that query `betterAuth.user`
   - Can't import directly to component tables via `convex import --table`
   - Must use export/modify/import workflow for manual changes

## Solution: Creating Users Manually

### Step 1: Create Better Auth user (via API)
```bash
curl -X POST "https://yourapp.com/api/auth/sign-up/email" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@example.com",
    "password": "your-password",
    "name": "Admin User"
  }'
```

### Step 2: Create app user (via Convex mutation)
```typescript
// convex/adminSetup.ts
export const createAdminUser = mutation({
  args: { email: v.string(), name: v.string(), setupSecret: v.string() },
  handler: async (ctx, args) => {
    // Verify secret
    if (args.setupSecret !== process.env.ADMIN_SECRET) {
      throw new Error("Unauthorized");
    }

    const userId = await ctx.db.insert("users", {
      email: args.email,
      name: args.name,
      isAdmin: true,
      subscriptionStatus: "ACTIVE",
    });
    return { userId };
  },
});
```

### Step 3: Run the mutation
```bash
npx convex run --prod adminSetup:createAdminUser \
  '{"email": "admin@example.com", "name": "Admin", "setupSecret": "your-secret"}'
```

## Solution: Deleting/Modifying Better Auth Users

Since you can't access component tables directly:

1. **Export production data**
   ```bash
   npx convex export --prod --path /tmp/convex-export
   ```

2. **Extract and modify**
   ```bash
   unzip /tmp/convex-export -d /tmp/convex-data
   # Edit _components/betterAuth/user/documents.jsonl
   # Edit _components/betterAuth/account/documents.jsonl
   ```

3. **Reimport**
   ```bash
   # Recreate zip with modifications
   cd /tmp/convex-data && zip -r ../convex-modified.zip .
   npx convex import --prod --replace-all -y /tmp/convex-modified.zip
   ```

## Verification
- Check both tables when debugging: `users/documents.jsonl` AND `_components/betterAuth/user/documents.jsonl`
- User must exist in BOTH locations for full functionality
- Better Auth user allows authentication
- App user allows business logic (admin access, subscriptions, etc.)

## Notes
- The `syncFromAuth` pattern is common: on first login, copy Better Auth user to app table
- ADMIN_EMAIL env var should be set in Convex (via `npx convex env set`) not just .env.local
- Password hashes in `betterAuth.account` use format `salt:hash` (scrypt-based)
- Turnstile CAPTCHA may be client-side only - API calls can bypass it

## Related Issues
- INTERNAL_API_SECRET must be set in BOTH Convex AND your hosting platform (Railway/Vercel)
- Magic link and password reset both require user to exist in Better Auth first

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/strataga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
