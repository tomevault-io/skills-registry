---
name: error-handling
description: Error handling patterns ve best practices. HATA YÖNETİMİ, EXCEPTION HANDLING veya ERROR BOUNDARY implementasyonu yaparken otomatik uygula. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Error Handling Standards

## Error Tipleri Hiyerarşisi

```typescript
// Base Error
class AppError extends Error {
  constructor(
    message: string,
    public code: string,
    public statusCode: number = 500,
    public details?: Record<string, unknown>
  ) {
    super(message)
    this.name = this.constructor.name
    Error.captureStackTrace(this, this.constructor)
  }
}

// Spesifik Error'lar
class ValidationError extends AppError {
  constructor(message: string, details?: Record<string, unknown>) {
    super(message, 'VALIDATION_ERROR', 400, details)
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id?: string) {
    super(`${resource} not found${id ? `: ${id}` : ''}`, 'NOT_FOUND', 404)
  }
}

class UnauthorizedError extends AppError {
  constructor(message = 'Authentication required') {
    super(message, 'UNAUTHORIZED', 401)
  }
}

class ForbiddenError extends AppError {
  constructor(message = 'Access denied') {
    super(message, 'FORBIDDEN', 403)
  }
}

class ConflictError extends AppError {
  constructor(message: string) {
    super(message, 'CONFLICT', 409)
  }
}

class RateLimitError extends AppError {
  constructor(retryAfter?: number) {
    super('Rate limit exceeded', 'RATE_LIMIT', 429, { retryAfter })
  }
}
```

## Try-Catch Patterns

### ✅ Doğru Kullanım

```typescript
// 1. Spesifik Error Handling
async function getUser(id: string): Promise<User> {
  try {
    const user = await userRepository.findById(id)
    if (!user) {
      throw new NotFoundError('User', id)
    }
    return user
  } catch (error) {
    if (error instanceof NotFoundError) {
      throw error // Re-throw known errors
    }
    logger.error('Unexpected error in getUser', { error, userId: id })
    throw new AppError('Failed to fetch user', 'USER_FETCH_FAILED')
  }
}

// 2. Error Transformation
async function createOrder(data: OrderInput): Promise<Order> {
  try {
    return await orderService.create(data)
  } catch (error) {
    if (error instanceof DatabaseError) {
      if (error.code === 'UNIQUE_VIOLATION') {
        throw new ConflictError('Order already exists')
      }
    }
    throw error
  }
}
```

### ❌ Yanlış Kullanım

```typescript
// 1. Error Yutma
try {
  await riskyOperation()
} catch {
  // YANLIŞ: Hata sessizce yutuldu
}

// 2. Generic Error
throw new Error('Something went wrong') // YANLIŞ: Belirsiz

// 3. Console.log ile Error
try {
  await operation()
} catch (error) {
  console.log(error) // YANLIŞ: Logger kullan
}
```

## Global Error Handler (Express)

```typescript
// middleware/error-handler.ts
import { Request, Response, NextFunction } from 'express'
import { AppError } from '@/errors'
import { logger } from '@/utils/logger'

export function errorHandler(
  error: Error,
  req: Request,
  res: Response,
  _next: NextFunction
): void {
  // Log error
  logger.error('Request error', {
    error: error.message,
    stack: error.stack,
    path: req.path,
    method: req.method,
    body: req.body,
    userId: req.user?.id,
  })

  // AppError ise
  if (error instanceof AppError) {
    res.status(error.statusCode).json({
      success: false,
      error: {
        code: error.code,
        message: error.message,
        details: error.details,
      },
    })
    return
  }

  // Validation Error (Zod, Joi, vb.)
  if (error.name === 'ZodError') {
    res.status(400).json({
      success: false,
      error: {
        code: 'VALIDATION_ERROR',
        message: 'Validation failed',
        details: error.issues,
      },
    })
    return
  }

  // Unknown error - 500
  res.status(500).json({
    success: false,
    error: {
      code: 'INTERNAL_ERROR',
      message: process.env.NODE_ENV === 'production' 
        ? 'Internal server error' 
        : error.message,
    },
  })
}
```

## Async Error Wrapper

```typescript
// utils/async-handler.ts
import { Request, Response, NextFunction } from 'express'

type AsyncHandler = (
  req: Request,
  res: Response,
  next: NextFunction
) => Promise<void>

export const asyncHandler = (fn: AsyncHandler) => {
  return (req: Request, res: Response, next: NextFunction) => {
    Promise.resolve(fn(req, res, next)).catch(next)
  }
}

// Kullanım
router.get('/users/:id', asyncHandler(async (req, res) => {
  const user = await userService.getById(req.params.id)
  res.json({ success: true, data: user })
}))
```

## Frontend Error Boundary (React)

```tsx
// components/ErrorBoundary.tsx
import { Component, ReactNode } from 'react'

interface Props {
  children: ReactNode
  fallback?: ReactNode
}

interface State {
  hasError: boolean
  error?: Error
}

class ErrorBoundary extends Component<Props, State> {
  state: State = { hasError: false }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error }
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    logger.error('React Error Boundary', { error, errorInfo })
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <ErrorFallback error={this.state.error} />
    }
    return this.props.children
  }
}
```

## Logging Best Practices

```typescript
// ✅ Context ile logla
logger.error('Failed to process payment', {
  orderId: order.id,
  userId: user.id,
  amount: order.total,
  error: error.message,
  stack: error.stack,
})

// ❌ Sadece error
logger.error(error) // Context yok!
```

## Error Code Conventions

| Prefix | Domain | Örnek |
|--------|--------|-------|
| `AUTH_` | Authentication | `AUTH_INVALID_TOKEN` |
| `USER_` | User operations | `USER_NOT_FOUND` |
| `ORDER_` | Order operations | `ORDER_ALREADY_PAID` |
| `VAL_` | Validation | `VAL_INVALID_EMAIL` |
| `DB_` | Database | `DB_CONNECTION_FAILED` |
| `EXT_` | External service | `EXT_PAYMENT_FAILED` |

## Checklist

- [ ] Custom Error class'ları tanımlı
- [ ] Global error handler var
- [ ] Async handler wrapper kullanılıyor
- [ ] Error'lar context ile loglanıyor
- [ ] Frontend'de Error Boundary var
- [ ] Error code convention takip ediliyor

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
