---
name: api-patterns
description: REST API design patterns, authentication, and error handling for this SaaS application Use when this capability is needed.
metadata:
  author: naoyatakashima
---

# API Patterns

## Authentication

### JWT Token Structure

```typescript
interface JWTPayload {
  sub: string;        // User ID
  email: string;
  role: 'user' | 'admin';
  iat: number;
  exp: number;
}
```

### Auth Middleware

```typescript
// Always use authMiddleware for protected routes
import { authMiddleware } from '@/middleware/auth';

router.get('/protected', authMiddleware, handler);
```

## Error Handling

### Standard Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| AUTH_REQUIRED | 401 | Missing or invalid token |
| FORBIDDEN | 403 | Insufficient permissions |
| NOT_FOUND | 404 | Resource not found |
| VALIDATION_ERROR | 400 | Invalid request body |
| RATE_LIMITED | 429 | Too many requests |
| INTERNAL_ERROR | 500 | Server error |

### Error Response Format

```typescript
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": { "field": "email" }
  }
}
```

## Pagination

### Request

```
GET /api/users?page=1&limit=20&sort=createdAt&order=desc
```

### Response

```typescript
{
  "data": [...],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}
```

## Rate Limiting

- Authenticated: 1000 requests/hour
- Unauthenticated: 100 requests/hour
- Endpoint-specific limits in `src/config/rateLimit.ts`

## Validation

Use Zod for all request validation:

```typescript
import { z } from 'zod';

const createUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(2).max(100),
  password: z.string().min(8),
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/naoyatakashima) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
