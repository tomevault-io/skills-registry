---
name: clerk-nextjs-patterns
description: Advanced Next.js patterns - middleware, Server Actions, caching with Clerk. Use when this capability is needed.
metadata:
  author: growupanand
---

# Next.js Patterns

For basic setup, see `setup/`.

## Impact Levels

- **CRITICAL** - Breaking bugs, security holes
- **HIGH** - Common mistakes
- **MEDIUM** - Optimization

## References

| Reference | Impact |
|-----------|--------|
| `references/server-vs-client.md` | CRITICAL - `await auth()` vs hooks |
| `references/middleware-strategies.md` | HIGH - Public-first vs protected-first |
| `references/server-actions.md` | HIGH - Protect mutations |
| `references/api-routes.md` | HIGH - 401 vs 403 |
| `references/caching-auth.md` | MEDIUM - User-scoped caching |

## Mental Model

Server vs Client = different auth APIs:
- **Server**: `await auth()` from `@clerk/nextjs/server` (async!)
- **Client**: `useAuth()` hook from `@clerk/nextjs` (sync)

Never mix them. Server Components use server imports, Client Components use hooks.

## Minimal Pattern

```typescript
// Server Component
import { auth } from '@clerk/nextjs/server'

export default async function Page() {
  const { userId } = await auth()  // MUST await!
  if (!userId) return <p>Not signed in</p>
  return <p>Hello {userId}</p>
}
```

## Common Pitfalls

| Symptom | Cause | Fix |
|---------|-------|-----|
| `undefined` userId in Server Component | Missing `await` | `await auth()` not `auth()` |
| Auth not working on API routes | Missing matcher | Add `'/(api|trpc)(.*)'` to middleware |
| Cache returns wrong user's data | Missing userId in key | Include `userId` in `unstable_cache` key |
| Mutations bypass auth | Unprotected Server Action | Check `auth()` at start of action |
| Wrong HTTP error code | Confused 401/403 | 401 = not signed in, 403 = no permission |

## See Also

- `setup/`
- `managing-orgs/`

## Docs

[Next.js SDK](https://clerk.com/docs/reference/nextjs/overview)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/growupanand) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
