---
name: security-audit
description: Use when performing security reviews or hardening an application. Covers OWASP top 10 mitigations, input validation, injection prevention, authentication, secrets management, and dependency scanning.
metadata:
  author: canivel
---

# Security Review Checklist

## Injection Prevention

### SQL Injection

```ts
// VULNERABLE: string concatenation
const result = await db.execute(`SELECT * FROM users WHERE email = '${email}'`);

// SAFE: parameterized queries (Drizzle handles this automatically)
const result = await db.select().from(users).where(eq(users.email, email));

// SAFE: raw SQL with parameters when needed
const result = await db.execute(sql`SELECT * FROM users WHERE email = ${email}`);
```

### XSS Prevention

```tsx
// React escapes by default. These are safe:
<p>{userInput}</p>
<div>{comment.body}</div>

// DANGEROUS: never use dangerouslySetInnerHTML with user input
// BAD:
<div dangerouslySetInnerHTML={{ __html: userComment }} />

// If you must render HTML, sanitize with DOMPurify:
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userComment) }} />

// Set Content-Security-Policy headers
// next.config.js
const securityHeaders = [
  { key: 'Content-Security-Policy', value: "default-src 'self'; script-src 'self'" },
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'X-Frame-Options', value: 'DENY' },
  { key: 'X-XSS-Protection', value: '1; mode=block' },
  { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
];
```

## Input Validation

```ts
// Validate ALL input at the API boundary with Zod
import { z } from 'zod';

const createUserSchema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(1).max(100).regex(/^[a-zA-Z\s'-]+$/),
  age: z.number().int().min(13).max(150),
  url: z.string().url().optional(),
});

// Validate file uploads
const uploadSchema = z.object({
  mimetype: z.enum(['image/jpeg', 'image/png', 'image/webp']),
  size: z.number().max(5 * 1024 * 1024), // 5MB limit
});

// Never trust Content-Type headers alone for file validation
// Verify magic bytes or use a library like file-type
```

## CSRF Protection

```ts
// For cookie-based auth, use CSRF tokens
import csrf from 'csurf';
app.use(csrf({ cookie: { httpOnly: true, sameSite: 'strict' } }));

// For token-based auth (Bearer tokens), CSRF is not a concern
// because browsers don't auto-attach Authorization headers

// Always set SameSite on cookies
res.cookie('session', token, {
  httpOnly: true,
  secure: true,          // HTTPS only
  sameSite: 'strict',    // or 'lax' for GET navigation
  maxAge: 24 * 60 * 60 * 1000,
  path: '/',
});
```

## Authentication and Authorization

```ts
// Password hashing: use bcrypt or argon2, never SHA/MD5
import bcrypt from 'bcrypt';
const hash = await bcrypt.hash(password, 12); // cost factor >= 12
const isValid = await bcrypt.compare(inputPassword, storedHash);

// JWT best practices
const token = jwt.sign(
  { userId: user.id, role: user.role },
  process.env.JWT_SECRET!,
  { expiresIn: '15m', algorithm: 'HS256' }  // short-lived access tokens
);

// Always check authorization at the resource level
async function getProject(req: Request, res: Response) {
  const project = await db.select().from(projects).where(eq(projects.id, req.params.id));
  // ALWAYS verify ownership/permissions, never just authentication
  if (project.ownerId !== req.user.id && req.user.role !== 'admin') {
    throw new AppError(403, 'FORBIDDEN', 'You do not have access to this project');
  }
}
```

## Secrets Management

```bash
# NEVER commit secrets to version control
# .gitignore must include:
.env
.env.*
*.pem
*.key
credentials.json
```

```ts
// Validate secrets exist at startup
const requiredSecrets = ['JWT_SECRET', 'DATABASE_URL', 'SMTP_PASSWORD'];
for (const secret of requiredSecrets) {
  if (!process.env[secret]) {
    throw new Error(`Missing required secret: ${secret}`);
  }
}

// Use platform-specific secret stores in production:
// - Vercel: Environment Variables
// - AWS: SSM Parameter Store or Secrets Manager
// - Railway: Variables
```

## CORS Configuration

```ts
import cors from 'cors';

// NEVER use origin: '*' with credentials
// BAD:
app.use(cors({ origin: '*', credentials: true }));

// GOOD: explicit allowlist
app.use(cors({
  origin: ['https://otto.example.com', 'https://app.otto.example.com'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
}));
```

## Dependency Scanning

```bash
# Run regularly in CI
npm audit
npm audit --audit-level=high

# Use GitHub Dependabot or Snyk for automated scanning
```

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: npm
    directory: /
    schedule:
      interval: weekly
    open-pull-requests-limit: 10
```

## Rate Limiting

```ts
import rateLimit from 'express-rate-limit';

// General API rate limit
app.use('/api', rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,
  standardHeaders: true,
}));

// Stricter limit for auth endpoints
app.use('/api/auth', rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 10,
  message: { success: false, error: { code: 'RATE_LIMITED', message: 'Too many attempts' } },
}));
```

## Security Review Checklist

Before every deployment, verify:

1. All user input is validated with Zod schemas at the API boundary.
2. No raw SQL concatenation exists anywhere in the codebase.
3. Authentication is required on all non-public endpoints.
4. Authorization checks happen at the resource level, not just route level.
5. Secrets are not hardcoded or committed to version control.
6. CORS is configured with an explicit origin allowlist.
7. Rate limiting is enabled on authentication and public endpoints.
8. Dependencies have no known critical vulnerabilities (`npm audit`).
9. HTTP security headers are set (CSP, X-Frame-Options, etc.).
10. Cookies use `httpOnly`, `secure`, and `sameSite` flags.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
