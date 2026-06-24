---
name: api-design
description: Use when designing or implementing REST API endpoints with Express. Covers route structure, request validation with Zod, error handling, authentication, pagination, and consistent response formats.
metadata:
  author: canivel
---

# REST API Design with Express

## Consistent Response Format

Every endpoint must return this shape:

```ts
// Success
{ "success": true, "data": { ... } }
{ "success": true, "data": [...], "meta": { "page": 1, "limit": 20, "total": 87 } }

// Error
{ "success": false, "error": { "code": "VALIDATION_ERROR", "message": "...", "details": [...] } }
```

```ts
// helpers/response.ts
export function success<T>(res: Response, data: T, status = 200) {
  return res.status(status).json({ success: true, data });
}

export function paginated<T>(res: Response, data: T[], meta: PaginationMeta) {
  return res.status(200).json({ success: true, data, meta });
}

export function error(res: Response, code: string, message: string, status: number, details?: any) {
  return res.status(status).json({ success: false, error: { code, message, details } });
}
```

## Route Structure

```
src/
  routes/
    index.ts          # mounts all routers
    users.router.ts
    projects.router.ts
  middleware/
    auth.ts
    validate.ts
    errorHandler.ts
  controllers/
    users.controller.ts
    projects.controller.ts
```

```ts
// routes/index.ts
import { Router } from 'express';
import usersRouter from './users.router';
import projectsRouter from './projects.router';

const router = Router();
router.use('/users', usersRouter);
router.use('/projects', projectsRouter);
export default router;

// routes/users.router.ts
import { Router } from 'express';
import { authenticate, authorize } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { createUserSchema, updateUserSchema } from '../schemas/users';
import * as ctrl from '../controllers/users.controller';

const router = Router();
router.get('/', authenticate, ctrl.listUsers);
router.get('/:id', authenticate, ctrl.getUser);
router.post('/', authenticate, authorize('admin'), validate(createUserSchema), ctrl.createUser);
router.patch('/:id', authenticate, validate(updateUserSchema), ctrl.updateUser);
router.delete('/:id', authenticate, authorize('admin'), ctrl.deleteUser);
export default router;
```

## Request Validation with Zod

```ts
import { z } from 'zod';
import { Request, Response, NextFunction } from 'express';

// Schema definition
export const createUserSchema = z.object({
  body: z.object({
    email: z.string().email(),
    name: z.string().min(1).max(100),
    role: z.enum(['user', 'admin']).default('user'),
  }),
});

export const listUsersSchema = z.object({
  query: z.object({
    page: z.coerce.number().int().positive().default(1),
    limit: z.coerce.number().int().min(1).max(100).default(20),
    search: z.string().optional(),
    role: z.enum(['user', 'admin']).optional(),
  }),
});

// Validation middleware
export function validate(schema: z.ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    const result = schema.safeParse({ body: req.body, query: req.query, params: req.params });
    if (!result.success) {
      return error(res, 'VALIDATION_ERROR', 'Invalid request', 400, result.error.flatten());
    }
    req.validated = result.data;
    next();
  };
}
```

## Error Handling Middleware

```ts
// middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express';

export class AppError extends Error {
  constructor(public statusCode: number, public code: string, message: string) {
    super(message);
  }
}

export function errorHandler(err: Error, req: Request, res: Response, _next: NextFunction) {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      success: false,
      error: { code: err.code, message: err.message },
    });
  }

  console.error('Unhandled error:', err);
  return res.status(500).json({
    success: false,
    error: { code: 'INTERNAL_ERROR', message: 'An unexpected error occurred' },
  });
}

// Async wrapper to catch promise rejections
export function asyncHandler(fn: (req: Request, res: Response, next: NextFunction) => Promise<any>) {
  return (req: Request, res: Response, next: NextFunction) => fn(req, res, next).catch(next);
}
```

## Authentication Middleware

```ts
import jwt from 'jsonwebtoken';

export function authenticate(req: Request, res: Response, next: NextFunction) {
  const header = req.headers.authorization;
  if (!header?.startsWith('Bearer ')) {
    throw new AppError(401, 'UNAUTHORIZED', 'Missing or invalid token');
  }
  try {
    const payload = jwt.verify(header.slice(7), process.env.JWT_SECRET!) as JwtPayload;
    req.user = payload;
    next();
  } catch {
    throw new AppError(401, 'UNAUTHORIZED', 'Token expired or invalid');
  }
}

export function authorize(...roles: string[]) {
  return (req: Request, res: Response, next: NextFunction) => {
    if (!roles.includes(req.user.role)) {
      throw new AppError(403, 'FORBIDDEN', 'Insufficient permissions');
    }
    next();
  };
}
```

## Pagination and Filtering

```ts
export async function listUsers(req: Request, res: Response) {
  const { page, limit, search, role } = req.validated.query;
  const offset = (page - 1) * limit;

  let query = db.select().from(users);
  if (search) query = query.where(ilike(users.name, `%${search}%`));
  if (role) query = query.where(eq(users.role, role));

  const [data, [{ count }]] = await Promise.all([
    query.limit(limit).offset(offset),
    db.select({ count: sql<number>`count(*)` }).from(users),
  ]);

  return paginated(res, data, { page, limit, total: Number(count) });
}
```

## Anti-Patterns

- NEVER return raw database errors to clients. Wrap them in AppError.
- NEVER use `res.send` for API responses. Always use the structured JSON helpers.
- NEVER put business logic in route files. Extract it to controllers or services.
- NEVER trust client input. Validate and sanitize everything with Zod.
- NEVER use sequential IDs in URLs if they expose data enumeration risks. Use UUIDs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
