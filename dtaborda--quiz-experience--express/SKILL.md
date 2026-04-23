---
name: express
description: > Use when this capability is needed.
metadata:
  author: dtaborda
---

# Express.js Best Practices Skill

## When to Use

Use this skill when:
- Setting up a new Express server
- Creating REST API endpoints
- Building custom middleware
- Implementing error handling
- Structuring Express applications
- Configuring CORS, body parsing, etc.

## Critical Patterns

### Project Structure

**ALWAYS use layered architecture:**

```
backend/
├── src/
│   ├── app.ts                 # Express app setup
│   ├── server.ts              # Server entry point
│   ├── routes/                # Route handlers
│   │   ├── index.ts
│   │   ├── quiz.routes.ts
│   │   └── health.routes.ts
│   ├── controllers/           # Business logic
│   │   └── quiz.controller.ts
│   ├── services/              # Data access
│   │   └── quiz.service.ts
│   ├── middleware/            # Custom middleware
│   │   ├── error.middleware.ts
│   │   ├── logger.middleware.ts
│   │   └── validate.middleware.ts
│   ├── types/                 # TypeScript types
│   │   └── express.d.ts
│   └── utils/                 # Utilities
│       └── async-handler.ts
├── data/                      # Static data
│   └── quizzes/
└── package.json
```

### Application Setup

**app.ts (Express configuration):**

```typescript
import express, { type Express } from 'express'
import cors from 'cors'
import helmet from 'helmet'
import compression from 'compression'
import { errorMiddleware } from './middleware/error.middleware'
import { loggerMiddleware } from './middleware/logger.middleware'
import routes from './routes'

export function createApp(): Express {
  const app = express()

  // Security middleware
  app.use(helmet())
  app.use(cors())

  // Body parsing
  app.use(express.json())
  app.use(express.urlencoded({ extended: true }))

  // Compression
  app.use(compression())

  // Logging
  app.use(loggerMiddleware)

  // Routes
  app.use('/api', routes)

  // Error handling (MUST be last)
  app.use(errorMiddleware)

  return app
}
```

**server.ts (Server entry point):**

```typescript
import { createApp } from './app'

const PORT = process.env.PORT || 3001
const app = createApp()

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`)
})
```

### Route Structure

**routes/index.ts (Router aggregator):**

```typescript
import { Router } from 'express'
import quizRoutes from './quiz.routes'
import healthRoutes from './health.routes'

const router = Router()

router.use('/health', healthRoutes)
router.use('/quizzes', quizRoutes)

export default router
```

**routes/quiz.routes.ts:**

```typescript
import { Router } from 'express'
import * as quizController from '../controllers/quiz.controller'
import { validateRequest } from '../middleware/validate.middleware'
import { QuizIdSchema } from 'shared/schemas/quiz'

const router = Router()

// GET /api/quizzes - List all quizzes
router.get('/', quizController.getAllQuizzes)

// GET /api/quizzes/:id - Get single quiz
router.get(
  '/:id',
  validateRequest({ params: QuizIdSchema }),
  quizController.getQuizById
)

export default router
```

### Controller Pattern

**controllers/quiz.controller.ts:**

```typescript
import type { Request, Response, NextFunction } from 'express'
import * as quizService from '../services/quiz.service'
import { asyncHandler } from '../utils/async-handler'

// GET /api/quizzes
export const getAllQuizzes = asyncHandler(
  async (req: Request, res: Response) => {
    const quizzes = await quizService.getAllQuizzes()
    res.json(quizzes)
  }
)

// GET /api/quizzes/:id
export const getQuizById = asyncHandler(
  async (req: Request, res: Response, next: NextFunction) => {
    const { id } = req.params
    const quiz = await quizService.getQuizById(id)
    
    if (!quiz) {
      return next({ status: 404, message: 'Quiz not found' })
    }
    
    res.json(quiz)
  }
)
```

### Service Layer

**services/quiz.service.ts:**

```typescript
import fs from 'fs/promises'
import path from 'path'
import type { Quiz } from 'shared/types/quiz'
import { QuizSchema } from 'shared/schemas/quiz'

const QUIZ_DIR = path.join(__dirname, '../../data/quizzes')

export async function getAllQuizzes(): Promise<Quiz[]> {
  const files = await fs.readdir(QUIZ_DIR)
  const jsonFiles = files.filter(f => f.endsWith('.json'))
  
  const quizzes = await Promise.all(
    jsonFiles.map(async file => {
      const content = await fs.readFile(path.join(QUIZ_DIR, file), 'utf-8')
      const data = JSON.parse(content)
      return QuizSchema.parse(data)  // Validate with Zod
    })
  )
  
  return quizzes
}

export async function getQuizById(id: string): Promise<Quiz | null> {
  try {
    const filePath = path.join(QUIZ_DIR, `${id}.json`)
    const content = await fs.readFile(filePath, 'utf-8')
    const data = JSON.parse(content)
    return QuizSchema.parse(data)
  } catch (error) {
    if ((error as NodeJS.ErrnoException).code === 'ENOENT') {
      return null
    }
    throw error
  }
}
```

### Error Handling

**middleware/error.middleware.ts:**

```typescript
import type { Request, Response, NextFunction } from 'express'
import { ZodError } from 'zod'

interface AppError extends Error {
  status?: number
  errors?: unknown
}

export function errorMiddleware(
  err: AppError,
  req: Request,
  res: Response,
  next: NextFunction
) {
  // Zod validation errors
  if (err instanceof ZodError) {
    return res.status(400).json({
      error: 'Validation failed',
      details: err.errors,
    })
  }

  // Custom app errors
  const status = err.status || 500
  const message = err.message || 'Internal server error'

  console.error('Error:', err)

  res.status(status).json({
    error: message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack }),
  })
}
```

**utils/async-handler.ts:**

```typescript
import type { Request, Response, NextFunction, RequestHandler } from 'express'

export function asyncHandler(
  fn: (req: Request, res: Response, next: NextFunction) => Promise<void>
): RequestHandler {
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next)
  }
}
```

### Validation Middleware

**middleware/validate.middleware.ts:**

```typescript
import type { Request, Response, NextFunction } from 'express'
import type { ZodSchema } from 'zod'

interface ValidationSchemas {
  body?: ZodSchema
  params?: ZodSchema
  query?: ZodSchema
}

export function validateRequest(schemas: ValidationSchemas) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      if (schemas.body) {
        req.body = schemas.body.parse(req.body)
      }
      if (schemas.params) {
        req.params = schemas.params.parse(req.params)
      }
      if (schemas.query) {
        req.query = schemas.query.parse(req.query)
      }
      next()
    } catch (error) {
      next(error)
    }
  }
}
```

### Request Logging

**middleware/logger.middleware.ts:**

```typescript
import type { Request, Response, NextFunction } from 'express'

export function loggerMiddleware(
  req: Request,
  res: Response,
  next: NextFunction
) {
  const start = Date.now()
  
  res.on('finish', () => {
    const duration = Date.now() - start
    console.log(
      `${req.method} ${req.path} ${res.statusCode} - ${duration}ms`
    )
  })
  
  next()
}
```

## Best Practices

### ALWAYS:

1. **Separate concerns:**
   - Routes → Controllers → Services
   - Keep routes thin (routing only)
   - Keep controllers focused (orchestration)
   - Keep services pure (business logic)

2. **Use async/await with error handling:**
   ```typescript
   // ✅ Good
   export const getQuiz = asyncHandler(async (req, res) => {
     const quiz = await quizService.getQuizById(req.params.id)
     res.json(quiz)
   })
   
   // ❌ Bad
   export const getQuiz = (req, res) => {
     quizService.getQuizById(req.params.id)
       .then(quiz => res.json(quiz))
       .catch(err => res.status(500).json({ error: err.message }))
   }
   ```

3. **Validate all inputs with Zod:**
   ```typescript
   router.post(
     '/quizzes',
     validateRequest({ body: CreateQuizSchema }),
     quizController.createQuiz
   )
   ```

4. **Use proper HTTP status codes:**
   - 200: Success
   - 201: Created
   - 204: No content
   - 400: Bad request
   - 401: Unauthorized
   - 403: Forbidden
   - 404: Not found
   - 500: Internal server error

5. **Return consistent error format:**
   ```typescript
   {
     "error": "Quiz not found",
     "details": { ... }  // Optional
   }
   ```

### NEVER:

1. **Don't handle errors in routes:**
   ```typescript
   // ❌ Bad
   router.get('/quizzes/:id', (req, res) => {
     try {
       const quiz = getQuiz(req.params.id)
       res.json(quiz)
     } catch (error) {
       res.status(500).json({ error: error.message })
     }
   })
   
   // ✅ Good - let error middleware handle it
   router.get('/quizzes/:id', asyncHandler(async (req, res, next) => {
     const quiz = await getQuiz(req.params.id)
     if (!quiz) {
       return next({ status: 404, message: 'Quiz not found' })
     }
     res.json(quiz)
   }))
   ```

2. **Don't mix business logic in routes:**
   ```typescript
   // ❌ Bad
   router.get('/quizzes', async (req, res) => {
     const files = await fs.readdir('./data/quizzes')
     const quizzes = files.map(file => JSON.parse(fs.readFileSync(file)))
     res.json(quizzes)
   })
   
   // ✅ Good
   router.get('/quizzes', quizController.getAllQuizzes)
   ```

3. **Don't send raw errors to client:**
   ```typescript
   // ❌ Bad
   res.status(500).json({ error: error.stack })
   
   // ✅ Good
   res.status(500).json({ 
     error: 'Internal server error',
     ...(process.env.NODE_ENV === 'development' && { stack: error.stack })
   })
   ```

## Common Patterns

### CORS Configuration

```typescript
import cors from 'cors'

app.use(cors({
  origin: process.env.FRONTEND_URL || 'http://localhost:3000',
  credentials: true,
}))
```

### Rate Limiting

```typescript
import rateLimit from 'express-rate-limit'

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
})

app.use('/api/', limiter)
```

### Request ID Tracking

```typescript
import { randomUUID } from 'crypto'

app.use((req, res, next) => {
  req.id = randomUUID()
  res.setHeader('X-Request-ID', req.id)
  next()
})
```

### Health Check

```typescript
// routes/health.routes.ts
import { Router } from 'express'

const router = Router()

router.get('/', (req, res) => {
  res.json({
    status: 'ok',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
  })
})

export default router
```

### TypeScript Types for Express

```typescript
// types/express.d.ts
import type { Quiz } from 'shared/types/quiz'

declare global {
  namespace Express {
    interface Request {
      id?: string
      user?: {
        id: string
        username: string
      }
    }
  }
}
```

## Testing with Supertest

```typescript
import request from 'supertest'
import { describe, it, expect } from 'vitest'
import { createApp } from '../app'

describe('Quiz API', () => {
  const app = createApp()
  
  it('GET /api/quizzes should return array', async () => {
    const response = await request(app)
      .get('/api/quizzes')
      .expect(200)
      .expect('Content-Type', /json/)
    
    expect(response.body).toBeInstanceOf(Array)
  })
  
  it('GET /api/quizzes/:id should return 404 for non-existent', async () => {
    await request(app)
      .get('/api/quizzes/non-existent')
      .expect(404)
  })
})
```

## Environment Configuration

**.env file:**
```
PORT=3001
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
```

**Load with dotenv:**
```typescript
import dotenv from 'dotenv'
dotenv.config()

const PORT = process.env.PORT || 3001
```

## Security Best Practices

```typescript
import helmet from 'helmet'
import mongoSanitize from 'express-mongo-sanitize'
import xss from 'xss-clean'

// Helmet - security headers
app.use(helmet())

// Sanitize data
app.use(mongoSanitize())
app.use(xss())

// Limit body size
app.use(express.json({ limit: '10mb' }))
```

## Performance Optimization

```typescript
import compression from 'compression'

// Gzip compression
app.use(compression())

// Caching headers
app.use((req, res, next) => {
  if (req.method === 'GET') {
    res.set('Cache-Control', 'public, max-age=300')
  }
  next()
})
```

## Resources

- **Express Docs:** https://expressjs.com
- **Best Practices:** https://expressjs.com/en/advanced/best-practice-performance.html
- **Security:** https://expressjs.com/en/advanced/best-practice-security.html
- **Helmet:** https://helmetjs.github.io
- **Express TypeScript:** https://github.com/microsoft/TypeScript-Node-Starter

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtaborda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
