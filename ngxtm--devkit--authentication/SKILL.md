---
name: next-js-authentication
description: Secure token storage (HttpOnly Cookies) and Middleware patterns. Use when this capability is needed.
metadata:
  author: ngxtm
---

# Authentication & Token Management

## **Priority: P0 (CRITICAL)**

Use **HttpOnly Cookies** for token storage. **Never** use LocalStorage.

## Key Rules

1. **Storage**: Use `cookies().set()` with `httpOnly: true`, `secure: true`, `sameSite: 'lax'`.
   - _Reference_: [Auth Implementation](references/auth-implementation.md) (See "Setting Tokens").
2. **Access**: Read tokens in Server Components via `cookies().get()`.
   - _Reference_: [Auth Implementation](references/auth-implementation.md) (See "Reading Tokens").
3. **Protection**: Guard routes in `middleware.ts` before rendering.
   - _Reference_: [Auth Implementation](references/auth-implementation.md) (See "Middleware Protection").

## Anti-Pattern: LocalStorage

- **Security Risk**: Vulnerable to XSS.
- **Performance Hit**: Incompatible with Server Components (RSC). Forces client hydration and causes layout shift.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngxtm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
