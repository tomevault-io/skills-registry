---
name: typescript-security
description: Secure coding practices for building safe TypeScript applications. Use when this capability is needed.
metadata:
  author: ngxtm
---

# TypeScript Security

## **Priority: P0 (CRITICAL)**

Security standards for TypeScript applications based on OWASP guidelines.

## Implementation Guidelines

- **Validation**: Validate all inputs with `zod`/`joi`/`class-validator`.
- **Sanitization**: Use `DOMPurify` for HTML. Prevent XSS.
- **Secrets**: Use env vars. Never hardcode.
- **SQL Injection**: Use parameterized queries or ORMs (Prisma/TypeORM).
- **Auth**: Use `bcrypt` for hashing. Implement strict RBAC.
- **HTTPS**: Enforce HTTPS. Set `secure`, `httpOnly`, `sameSite` cookies.
- **Rate Limit**: Prevent brute-force/DDoS.
- **Deps**: Audit with `npm audit`.

## Anti-Patterns

- **No `eval()`**: Avoid dynamic execution.
- **No Plaintext**: Never commit secrets.
- **No Trust**: Validate everything server-side.

## Code

```typescript
// Validation (Zod)
const UserSchema = z.object({
  email: z.string().email(),
  pass: z.string().min(8),
});

// Secure Cookie
const cookieOpts = {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'prod',
  sameSite: 'strict' as const,
};
```

## Reference & Examples

For authentication patterns and security headers:
See [references/REFERENCE.md](references/REFERENCE.md).

## Related Topics

best-practices | language

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngxtm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
