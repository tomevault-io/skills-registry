---
name: web-security
description: Security best practices for web applications including XSS prevention, CSRF protection, and input validation. Use when implementing authentication, handling user input, configuring security headers, or when user asks about "security", "XSS", "CSRF", "SQL injection", or "authentication". Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Web Security

Build secure web applications by preventing common vulnerabilities.

## Input Validation

### Rules

- ✅ DO: Validate all user input on the server
- ✅ DO: Use allowlists (not denylists) for validation
- ✅ DO: Validate type, length, format, and range
- ✅ DO: Use schema validation libraries (Zod, Yup, Joi)
- ❌ DON'T: Trust client-side validation alone
- ❌ DON'T: Use regex for complex validation (email, URL)

### Examples

```typescript
import { z } from "zod";

// ✅ Good - schema validation
const UserSchema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(1).max(100),
  age: z.number().int().min(0).max(150),
});

function createUser(input: unknown) {
  const data = UserSchema.parse(input); // Throws if invalid
  // data is now typed and validated
}

// ✅ Allowlist approach
const ALLOWED_ROLES = ["admin", "user", "guest"] as const;
type Role = (typeof ALLOWED_ROLES)[number];

function setRole(role: string): Role {
  if (!ALLOWED_ROLES.includes(role as Role)) {
    throw new Error("Invalid role");
  }
  return role as Role;
}
```

## XSS Prevention

### Rules

- ✅ DO: Escape output based on context (HTML, JS, URL, CSS)
- ✅ DO: Use framework auto-escaping (React, Vue, Angular)
- ✅ DO: Use Content-Security-Policy headers
- ✅ DO: Sanitize HTML if you must allow it (DOMPurify)
- ❌ DON'T: Use `dangerouslySetInnerHTML` without sanitization
- ❌ DON'T: Use `eval()` or `new Function()` with user input
- ❌ DON'T: Insert user input into inline scripts

### Examples

```typescript
// ❌ Bad - XSS vulnerable
element.innerHTML = userInput;
document.write(userInput);

// ✅ Good - safe text content
element.textContent = userInput;

// ✅ Good - sanitize if HTML needed
import DOMPurify from 'dompurify';
element.innerHTML = DOMPurify.sanitize(userInput);

// ✅ React - sanitize before dangerouslySetInnerHTML
import DOMPurify from 'dompurify';

function SafeHTML({ html }: { html: string }) {
  return (
    <div
      dangerouslySetInnerHTML={{
        __html: DOMPurify.sanitize(html)
      }}
    />
  );
}

// CSP Header example
// Content-Security-Policy: default-src 'self'; script-src 'self'
```

## SQL Injection Prevention

### Rules

- ✅ DO: Use parameterized queries / prepared statements
- ✅ DO: Use ORM query builders
- ✅ DO: Escape identifiers (table/column names) if dynamic
- ❌ DON'T: Concatenate user input into SQL strings
- ❌ DON'T: Use string templates for SQL

### Examples

```typescript
// ❌ Bad - SQL injection
const query = `SELECT * FROM users WHERE id = '${userId}'`;

// ✅ Good - parameterized query
const query = "SELECT * FROM users WHERE id = $1";
await db.query(query, [userId]);

// ✅ Good - ORM query builder
const user = await prisma.user.findUnique({
  where: { id: userId },
});

// ✅ Good - escape identifiers if needed
import { escapeIdentifier } from "pg";
const column = escapeIdentifier(userColumn);
const query = `SELECT ${column} FROM users`;
```

## Authentication

### Rules

- ✅ DO: Use established auth libraries (next-auth, passport, lucia)
- ✅ DO: Hash passwords with bcrypt, scrypt, or Argon2
- ✅ DO: Use secure session management
- ✅ DO: Implement rate limiting on login
- ❌ DON'T: Store passwords in plain text
- ❌ DON'T: Use MD5 or SHA1 for passwords
- ❌ DON'T: Create custom auth systems unless necessary

### Examples

```typescript
import bcrypt from "bcrypt";

// ✅ Good - password hashing
const SALT_ROUNDS = 12;

async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

async function verifyPassword(
  password: string,
  hash: string,
): Promise<boolean> {
  return bcrypt.compare(password, hash);
}

// ✅ Good - timing-safe comparison for tokens
import crypto from "crypto";

function verifyToken(provided: string, expected: string): boolean {
  const a = Buffer.from(provided);
  const b = Buffer.from(expected);
  return a.length === b.length && crypto.timingSafeEqual(a, b);
}
```

## Authorization

### Rules

- ✅ DO: Check permissions on every request
- ✅ DO: Verify resource ownership before access
- ✅ DO: Use principle of least privilege
- ✅ DO: Fail closed (deny by default)
- ❌ DON'T: Rely on hidden URLs for security
- ❌ DON'T: Trust client-provided user IDs

### Examples

```typescript
// ❌ Bad - trusts client
async function updateNote(noteId: string, userId: string, content: string) {
  await db.notes.update(noteId, { content });
}

// ✅ Good - verifies ownership
async function updateNote(noteId: string, userId: string, content: string) {
  const note = await db.notes.findUnique(noteId);

  if (!note) {
    throw new NotFoundError("Note not found");
  }

  if (note.ownerId !== userId) {
    throw new ForbiddenError("Not authorized");
  }

  await db.notes.update(noteId, { content });
}

// ✅ Better - query with ownership check
async function updateNote(noteId: string, userId: string, content: string) {
  const result = await db.notes.updateMany({
    where: { id: noteId, ownerId: userId },
    data: { content },
  });

  if (result.count === 0) {
    throw new NotFoundError("Note not found or not authorized");
  }
}
```

## CSRF Prevention

### Rules

- ✅ DO: Use CSRF tokens for state-changing requests
- ✅ DO: Use SameSite cookie attribute
- ✅ DO: Verify Origin/Referer headers
- ❌ DON'T: Use GET for state-changing operations

### Examples

```typescript
// ✅ Good - CSRF token in form
<form method="POST" action="/transfer">
  <input type="hidden" name="csrf_token" value={csrfToken} />
  <button type="submit">Transfer</button>
</form>

// ✅ Good - SameSite cookies
res.cookie('session', sessionId, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
});
```

## Sensitive Data

### Rules

- ✅ DO: Use HTTPS everywhere
- ✅ DO: Store secrets in environment variables
- ✅ DO: Use httpOnly and secure flags for cookies
- ✅ DO: Mask sensitive data in logs
- ❌ DON'T: Commit secrets to version control
- ❌ DON'T: Log passwords, tokens, or PII
- ❌ DON'T: Store sensitive data in localStorage

### Examples

```typescript
// ✅ Good - environment variables
const apiKey = process.env.API_KEY;

// ✅ Good - mask in logs
function logRequest(req: Request) {
  const safeHeaders = { ...req.headers };
  if (safeHeaders.authorization) {
    safeHeaders.authorization = "[REDACTED]";
  }
  console.log("Request:", { url: req.url, headers: safeHeaders });
}

// ✅ Good - secure cookies
app.use(
  session({
    cookie: {
      httpOnly: true,
      secure: process.env.NODE_ENV === "production",
      sameSite: "strict",
      maxAge: 24 * 60 * 60 * 1000, // 1 day
    },
  }),
);
```

## Security Headers

### Recommended Headers

```typescript
// Content-Security-Policy
"default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'"

// Other security headers
{
  'X-Content-Type-Options': 'nosniff',
  'X-Frame-Options': 'DENY',
  'X-XSS-Protection': '1; mode=block',
  'Referrer-Policy': 'strict-origin-when-cross-origin',
  'Permissions-Policy': 'camera=(), microphone=(), geolocation=()',
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
