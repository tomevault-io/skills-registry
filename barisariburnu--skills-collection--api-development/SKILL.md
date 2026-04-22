---
name: api-development
description: Complete API development guide for Next.js 15 App Router with REST endpoints, error handling, validation, authentication, security best practices, and performance optimization. Use when creating API routes, handling requests, building CRUD operations, or implementing API features. Use when this capability is needed.
metadata:
  author: barisariburnu
---

# API Development Skill

**Skill Location**: `{project_path}/skills/api-development/`

Comprehensive guide for building production-ready APIs in Next.js 15 App Router, optimized for minimal token usage while maintaining security and performance.

---

## When to Use This Skill (Trigger Patterns)

**MUST apply this skill when:**
- Creating API routes in Next.js App Router
- Implementing REST endpoints (GET, POST, PUT, DELETE)
- Handling error responses
- Validating request data
- Adding authentication/authorization
- Securing API routes
- Optimizing API performance
- Implementing pagination, filtering, sorting

**Trigger phrases:**
- "create an API endpoint"
- "add API route"
- "handle POST/GET/PUT/DELETE"
- "validate API requests"
- "add authentication to API"
- "secure API route"

---

## Core Principles

### 1. RESTful Design

```
GET    /api/resource     - List all
GET    /api/resource/:id - Get by ID
POST   /api/resource     - Create new
PUT    /api/resource/:id - Update by ID
DELETE /api/resource/:id - Delete by ID
```

### 2. Standard Response Format

```typescript
// Success
{
  "success": true,
  "data": { ... }
}

// Error
{
  "success": false,
  "error": {
    "message": "Error message",
    "code": "ERROR_CODE"
  }
}
```

### 3. HTTP Status Codes

- `200` - OK (successful GET, PUT, PATCH)
- `201` - Created (successful POST)
- `400` - Bad Request (validation errors)
- `401` - Unauthorized (not logged in)
- `403` - Forbidden (no permission)
- `404` - Not Found
- `500` - Internal Server Error

---

## Token-Saving Strategies

### 1. Use Standard Patterns Without Explanation

**❌ INEFFICIENT:**
```
"This function handles the GET request and returns JSON..."
```

**✅ EFFICIENT:**
```
// Standard GET handler
```

### 2. Minimize Error Handling Boilerplate

**❌ INEFFICIENT:** Write full try-catch every time

**✅ EFFICIENT:** Create utility functions

```typescript
// src/lib/api-response.ts
export const success = (data: any, status = 200) =>
  NextResponse.json({ success: true, data }, { status })

export const error = (message: string, status = 500, code?: string) =>
  NextResponse.json(
    { success: false, error: { message, code } },
    { status }
  )
```

### 3. Assume Knowledge of Basics

Don't explain:
- What `NextRequest`, `NextResponse` are
- How HTTP methods work
- Basic error handling concepts

Focus on:
- Implementation patterns
- Security considerations
- Performance optimization

---

## API Route Templates

### 1. Basic CRUD Routes

```typescript
import { NextRequest, NextResponse } from 'next/server'
import { db } from '@/lib/db'
import { success, error } from '@/lib/api-response'

// GET /api/users
export async function GET(req: NextRequest) {
  try {
    const { searchParams } = new URL(req.url)
    const page = parseInt(searchParams.get('page') || '1')
    const limit = parseInt(searchParams.get('limit') || '10')

    const [users, total] = await Promise.all([
      db.user.findMany({
        skip: (page - 1) * limit,
        take: limit,
        orderBy: { createdAt: 'desc' }
      }),
      db.user.count()
    ])

    return success({
      users,
      pagination: { page, limit, total }
    })
  } catch (e) {
    return error('Internal error', 500)
  }
}

// POST /api/users
export async function POST(req: NextRequest) {
  try {
    const body = await req.json()
    const user = await db.user.create({ data: body })
    return success(user, 201)
  } catch (e) {
    return error('Failed to create user', 500)
  }
}
```

### 2. Single Resource Routes

```typescript
// src/app/api/users/[id]/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { db } from '@/lib/db'
import { success, error } from '@/lib/api-response'

// GET /api/users/[id]
export async function GET(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const user = await db.user.findUnique({
      where: { id: params.id }
    })

    if (!user) {
      return error('User not found', 404, 'NOT_FOUND')
    }

    return success(user)
  } catch (e) {
    return error('Internal error', 500)
  }
}

// PUT /api/users/[id]
export async function PUT(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const body = await req.json()
    const user = await db.user.update({
      where: { id: params.id },
      data: body
    })
    return success(user)
  } catch (e) {
    return error('Failed to update user', 500)
  }
}

// DELETE /api/users/[id]
export async function DELETE(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    await db.user.delete({
      where: { id: params.id }
    })
    return success({ success: true })
  } catch (e) {
    return error('Failed to delete user', 500)
  }
}
```

---

## Validation Patterns

### 1. Zod Validation

```typescript
import { z } from 'zod'

// src/lib/validations.ts
export const userSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email address'),
  role: z.enum(['USER', 'ADMIN']).default('USER')
})

// Usage in API
export async function POST(req: NextRequest) {
  try {
    const body = await req.json()
    const validated = userSchema.parse(body)

    const user = await db.user.create({ data: validated })
    return success(user, 201)
  } catch (e) {
    if (e instanceof z.ZodError) {
      return error(e.errors[0].message, 400, 'VALIDATION_ERROR')
    }
    return error('Internal error', 500)
  }
}
```

### 2. Query Parameter Validation

```typescript
export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url)

  const page = Math.max(1, parseInt(searchParams.get('page') || '1'))
  const limit = Math.min(100, Math.max(1, parseInt(searchParams.get('limit') || '10')))
  const sort = searchParams.get('sort') || 'createdAt'
  const order = searchParams.get('order') === 'asc' ? 'asc' : 'desc'

  // ... use validated params
}
```

---

## Error Handling

### 1. Centralized Error Handler

```typescript
// src/lib/api-error.ts
export class ApiError extends Error {
  constructor(
    public message: string,
    public status: number,
    public code?: string
  ) {
    super(message)
    this.name = 'ApiError'
  }
}

export function handleApiError(e: unknown): NextResponse {
  if (e instanceof ApiError) {
    return error(e.message, e.status, e.code)
  }

  if (e instanceof z.ZodError) {
    return error(e.errors[0].message, 400, 'VALIDATION_ERROR')
  }

  console.error('Unhandled error:', e)
  return error('Internal server error', 500)
}

// Usage
export async function POST(req: NextRequest) {
  try {
    // ... logic
  } catch (e) {
    return handleApiError(e)
  }
}
```

### 2. Custom Errors

```typescript
// Not found
throw new ApiError('User not found', 404, 'NOT_FOUND')

// Validation failed
throw new ApiError('Invalid email format', 400, 'VALIDATION_ERROR')

// Unauthorized
throw new ApiError('Authentication required', 401, 'UNAUTHORIZED')

// Forbidden
throw new ApiError('Insufficient permissions', 403, 'FORBIDDEN')
```

---

## Authentication & Authorization

### 1. Basic Auth Check

```typescript
// src/lib/auth.ts
import { cookies } from 'next/headers'

export async function getCurrentUser() {
  const cookieStore = await cookies()
  const token = cookieStore.get('auth-token')

  if (!token) {
    throw new ApiError('Unauthorized', 401, 'UNAUTHORIZED')
  }

  // Verify token and return user
  const user = await verifyToken(token.value)
  return user
}

// Usage in API
export async function GET(req: NextRequest) {
  try {
    const user = await getCurrentUser()
    // ... use user data
  } catch (e) {
    return handleApiError(e)
  }
}
```

### 2. Role-Based Access Control

```typescript
// src/lib/auth.ts
export function requireRole(user: User, ...roles: string[]) {
  if (!roles.includes(user.role)) {
    throw new ApiError(
      'Insufficient permissions',
      403,
      'FORBIDDEN'
    )
  }
}

// Usage
export async function DELETE(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const user = await getCurrentUser()
    requireRole(user, 'ADMIN')

    await db.user.delete({ where: { id: params.id } })
    return success({ success: true })
  } catch (e) {
    return handleApiError(e)
  }
}
```

### 3. Resource Ownership Check

```typescript
export async function checkOwnership(
  user: User,
  resource: any,
  ownerField: string = 'userId'
) {
  if (resource[ownerField] !== user.id && user.role !== 'ADMIN') {
    throw new ApiError('Access denied', 403, 'FORBIDDEN')
  }
}

// Usage
export async function PUT(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    const user = await getCurrentUser()
    const post = await db.post.findUnique({
      where: { id: params.id }
    })

    if (!post) {
      throw new ApiError('Post not found', 404, 'NOT_FOUND')
    }

    checkOwnership(user, post)
    // ... update
  } catch (e) {
    return handleApiError(e)
  }
}
```

---

## Advanced Patterns

### 1. Pagination

```typescript
export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url)
  const page = parseInt(searchParams.get('page') || '1')
  const limit = parseInt(searchParams.get('limit') || '10')

  const [items, total] = await Promise.all([
    db.item.findMany({
      skip: (page - 1) * limit,
      take: limit,
      orderBy: { createdAt: 'desc' }
    }),
    db.item.count()
  ])

  return success({
    items,
    pagination: {
      page,
      limit,
      total,
      pages: Math.ceil(total / limit),
      hasNext: page * limit < total,
      hasPrev: page > 1
    }
  })
}
```

### 2. Filtering

```typescript
export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url)

  const filters: any = {}

  // Text search
  const search = searchParams.get('search')
  if (search) {
    filters.OR = [
      { name: { contains: search } },
      { email: { contains: search } }
    ]
  }

  // Exact matches
  const status = searchParams.get('status')
  if (status) {
    filters.status = status
  }

  // Date range
  const from = searchParams.get('from')
  const to = searchParams.get('to')
  if (from || to) {
    filters.createdAt = {}
    if (from) filters.createdAt.gte = new Date(from)
    if (to) filters.createdAt.lte = new Date(to)
  }

  const items = await db.item.findMany({
    where: filters
  })

  return success(items)
}
```

### 3. Sorting

```typescript
export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url)
  const sortBy = searchParams.get('sortBy') || 'createdAt'
  const sortOrder = searchParams.get('order') || 'desc'

  const items = await db.item.findMany({
    orderBy: {
      [sortBy]: sortOrder
    }
  })

  return success(items)
}
```

### 4. File Upload

```typescript
export async function POST(req: NextRequest) {
  try {
    const formData = await req.formData()
    const file = formData.get('file') as File

    if (!file) {
      throw new ApiError('No file uploaded', 400, 'NO_FILE')
    }

    // Validate file
    const maxSize = 5 * 1024 * 1024 // 5MB
    if (file.size > maxSize) {
      throw new ApiError('File too large', 400, 'FILE_TOO_LARGE')
    }

    const bytes = await file.arrayBuffer()
    const buffer = Buffer.from(bytes)

    // Save file or upload to cloud storage
    const url = await saveFile(buffer, file.name)

    return success({ url })
  } catch (e) {
    return handleApiError(e)
  }
}
```

---

## Performance Optimization

### 1. Caching

```typescript
// Cache-Control headers
export async function GET(req: NextRequest) {
  const data = await db.item.findMany()

  return NextResponse.json(
    { success: true, data },
    {
      headers: {
        'Cache-Control': 'public, s-maxage=60, stale-while-revalidate=300'
      }
    }
  )
}
```

### 2. Select Only Needed Fields

```typescript
// Bad: Fetches all fields
const users = await db.user.findMany()

// Good: Selects only needed fields
const users = await db.user.findMany({
  select: {
    id: true,
    name: true,
    email: true
  }
})
```

### 3. Use Prisma Transactions

```typescript
await db.$transaction([
  db.order.create({ data: orderData }),
  db.product.updateMany({
    where: { id: { in: productIds } },
    data: { stock: { decrement: 1 } }
  })
])
```

---

## Security Best Practices

### 1. Input Sanitization

```typescript
const schema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(2).max(100).trim()
})
```

### 2. Rate Limiting

```typescript
// Simple rate limiting with localStorage
// For production, use Redis or dedicated rate limiting
```

### 3. CORS

```typescript
export const OPTIONS = async (req: NextRequest) => {
  return new NextResponse(null, {
    status: 204,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type, Authorization'
    }
  })
}
```

### 4. SQL Injection Prevention

Prisma automatically handles SQL injection through parameterized queries:

```typescript
// Safe - Prisma handles escaping
const users = await db.user.findMany({
  where: {
    name: { contains: userInput } // Automatically escaped
  }
})
```

---

## Common Pitfalls & Solutions

### ❌ Problem: Missing Error Handling

**✅ Solution:** Always wrap in try-catch with centralized handler

### ❌ Problem: Not Validating Input

**✅ Solution:** Always use Zod schemas for validation

### ❌ Problem: Over-fetching Data

**✅ Solution:** Use `select` to fetch only needed fields

### ❌ Problem: Inconsistent Response Format

**✅ Solution:** Use `success()` and `error()` utility functions

---

## Token-Efficient Prompt Templates

### Create CRUD API
```
Create CRUD API for <RESOURCE>:
- Routes: GET, POST, PUT, DELETE
- Validation with Zod
- Standard response format
- Error handling
```

### Add Authentication
```
Add auth to /api/<resource>:
- Check authentication
- Add authorization for specific roles
- Handle auth errors
```

### Add Filtering/Sorting
```
Add filtering to GET /api/<resource>:
- Search by <fields>
- Filter by <fields>
- Sort by <fields>
- Pagination
```

---

## Quick Reference

```typescript
// Standard imports
import { NextRequest, NextResponse } from 'next/server'
import { db } from '@/lib/db'
import { z } from 'zod'

// Response helpers
const success = (data: any, status = 200) => NextResponse.json({ success: true, data }, { status })
const error = (message: string, status = 500) => NextResponse.json({ success: false, error: { message } }, { status })

// HTTP methods
export async function GET(req: NextRequest) { }
export async function POST(req: NextRequest) { }
export async function PUT(req: NextRequest, { params }: any) { }
export async function DELETE(req: NextRequest, { params }: any) { }

// Route params
export async function GET(req: NextRequest, { params }: { params: { id: string } }) {
  const id = params.id
}

// Query params
const { searchParams } = new URL(req.url)
const page = searchParams.get('page')
```

---

## Important Reminders

1. **Use API routes** - Prefer over server actions
2. **Validate everything** - Use Zod for all inputs
3. **Standard responses** - Use `success()` and `error()` helpers
4. **Handle errors** - Always use try-catch
5. **Select only needed fields** - Avoid over-fetching
6. **Add caching** - Use Cache-Control headers
7. **Secure endpoints** - Add auth where needed
8. **Consistent naming** - Follow REST conventions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/barisariburnu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
