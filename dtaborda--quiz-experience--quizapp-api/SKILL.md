---
name: quizapp-api
description: > Use when this capability is needed.
metadata:
  author: dtaborda
---

# QuizApp API Skill

## When to Use

Use this skill when:
- Creating new API endpoints for quizzes
- Loading quiz data from JSON files
- Implementing quiz-related backend features
- Structuring QuizApp backend code
- Validating quiz API requests with Zod
- Setting up Express routes for QuizApp

## Critical Patterns

### API Endpoints

**QuizApp API contract:**

```
GET  /api/health              → Health check
GET  /api/quizzes             → List all quizzes (summary)
GET  /api/quizzes/:quizId     → Get full quiz with questions
```

**ALWAYS return consistent JSON:**

```typescript
// GET /api/quizzes
[
  {
    "id": "agent-fundamentals",
    "title": "Agent Fundamentals",
    "description": "Test your knowledge of AI agent basics"
  }
]

// GET /api/quizzes/:quizId
{
  "id": "agent-fundamentals",
  "title": "Agent Fundamentals",
  "description": "Test your knowledge of AI agent basics",
  "questions": [
    {
      "id": "q1",
      "text": "What is an AI agent?",
      "options": ["A", "B", "C", "D"],
      "correctOption": "B",
      "explanation": "..."
    }
  ]
}
```

### File-Based Data Storage

**ALWAYS store quizzes as JSON files:**

```
backend/
└── data/
    └── quizzes/
        ├── agent-fundamentals.json
        ├── prompt-engineering.json
        └── model-selection.json
```

**Quiz JSON structure:**

```json
{
  "id": "agent-fundamentals",
  "title": "Agent Fundamentals",
  "description": "Test your knowledge of AI agent basics",
  "questions": [
    {
      "id": "q1",
      "text": "What is the primary purpose of an AI agent?",
      "options": [
        "To replace human workers",
        "To autonomously perform tasks",
        "To store large amounts of data",
        "To create visual interfaces"
      ],
      "correctOption": "To autonomously perform tasks",
      "explanation": "AI agents are designed to autonomously perform tasks and make decisions based on goals."
    }
  ]
}
```

### Service Layer for Quiz Data

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
      return QuizSchema.parse(data)
    })
  )
  
  return quizzes
}

export async function getQuizSummaries(): Promise<Array<{
  id: string
  title: string
  description: string
}>> {
  const quizzes = await getAllQuizzes()
  return quizzes.map(({ id, title, description }) => ({
    id,
    title,
    description,
  }))
}

export async function getQuizById(quizId: string): Promise<Quiz | null> {
  try {
    const filePath = path.join(QUIZ_DIR, `${quizId}.json`)
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

### Controller Layer

**controllers/quiz.controller.ts:**

```typescript
import type { Request, Response, NextFunction } from 'express'
import * as quizService from '../services/quiz.service'
import { asyncHandler } from '../utils/async-handler'

// GET /api/quizzes
export const getAllQuizzes = asyncHandler(
  async (req: Request, res: Response) => {
    const quizzes = await quizService.getQuizSummaries()
    res.json(quizzes)
  }
)

// GET /api/quizzes/:quizId
export const getQuizById = asyncHandler(
  async (req: Request, res: Response, next: NextFunction) => {
    const { quizId } = req.params
    const quiz = await quizService.getQuizById(quizId)
    
    if (!quiz) {
      return next({
        status: 404,
        message: `Quiz '${quizId}' not found`,
      })
    }
    
    res.json(quiz)
  }
)
```

### Route Setup

**routes/quiz.routes.ts:**

```typescript
import { Router } from 'express'
import * as quizController from '../controllers/quiz.controller'
import { validateRequest } from '../middleware/validate.middleware'
import { z } from 'zod'

const router = Router()

// Validation schemas
const QuizIdSchema = z.object({
  quizId: z.string().min(1),
})

// GET /api/quizzes - List all quizzes
router.get('/', quizController.getAllQuizzes)

// GET /api/quizzes/:quizId - Get single quiz
router.get(
  '/:quizId',
  validateRequest({ params: QuizIdSchema }),
  quizController.getQuizById
)

export default router
```

### Main Router

**routes/index.ts:**

```typescript
import { Router } from 'express'
import quizRoutes from './quiz.routes'
import healthRoutes from './health.routes'

const router = Router()

router.use('/health', healthRoutes)
router.use('/quizzes', quizRoutes)

export default router
```

### Health Check Endpoint

**routes/health.routes.ts:**

```typescript
import { Router } from 'express'

const router = Router()

router.get('/', (req, res) => {
  res.json({
    status: 'ok',
    timestamp: new Date().toISOString(),
    service: 'QuizApp API',
  })
})

export default router
```

## Zod Schema Validation

**ALWAYS validate quiz data with Zod:**

```typescript
// shared/schemas/quiz.ts
import { z } from 'zod'

export const QuestionSchema = z.object({
  id: z.string(),
  text: z.string(),
  options: z.array(z.string()).min(2),
  correctOption: z.string(),
  explanation: z.string(),
})

export const QuizSchema = z.object({
  id: z.string(),
  title: z.string(),
  description: z.string(),
  questions: z.array(QuestionSchema).min(1),
})

export type Quiz = z.infer<typeof QuizSchema>
export type Question = z.infer<typeof QuestionSchema>
```

**Use in service layer:**

```typescript
const data = JSON.parse(content)
const quiz = QuizSchema.parse(data)  // Validates and throws if invalid
```

## Error Handling

**QuizApp-specific error responses:**

```typescript
// 404 - Quiz not found
{
  "error": "Quiz 'non-existent' not found"
}

// 400 - Invalid quiz ID
{
  "error": "Validation failed",
  "details": [
    {
      "path": ["quizId"],
      "message": "Required"
    }
  ]
}

// 500 - Internal server error
{
  "error": "Internal server error"
}
```

## Testing API Endpoints

```typescript
import request from 'supertest'
import { describe, it, expect } from 'vitest'
import { createApp } from '../app'

describe('Quiz API', () => {
  const app = createApp()
  
  describe('GET /api/quizzes', () => {
    it('should return array of quiz summaries', async () => {
      const response = await request(app)
        .get('/api/quizzes')
        .expect(200)
        .expect('Content-Type', /json/)
      
      expect(response.body).toBeInstanceOf(Array)
      expect(response.body[0]).toHaveProperty('id')
      expect(response.body[0]).toHaveProperty('title')
      expect(response.body[0]).toHaveProperty('description')
      expect(response.body[0]).not.toHaveProperty('questions')
    })
  })
  
  describe('GET /api/quizzes/:quizId', () => {
    it('should return full quiz with questions', async () => {
      const response = await request(app)
        .get('/api/quizzes/agent-fundamentals')
        .expect(200)
      
      expect(response.body).toHaveProperty('id', 'agent-fundamentals')
      expect(response.body).toHaveProperty('questions')
      expect(response.body.questions).toBeInstanceOf(Array)
      expect(response.body.questions.length).toBeGreaterThan(0)
    })
    
    it('should return 404 for non-existent quiz', async () => {
      const response = await request(app)
        .get('/api/quizzes/non-existent')
        .expect(404)
      
      expect(response.body).toHaveProperty('error')
      expect(response.body.error).toContain('not found')
    })
    
    it('should validate quiz ID format', async () => {
      await request(app)
        .get('/api/quizzes/')
        .expect(404)  // Empty ID
    })
  })
  
  describe('GET /api/health', () => {
    it('should return health status', async () => {
      const response = await request(app)
        .get('/api/health')
        .expect(200)
      
      expect(response.body).toHaveProperty('status', 'ok')
      expect(response.body).toHaveProperty('timestamp')
    })
  })
})
```

## Common Patterns

### Adding New Quiz

1. **Create JSON file:**
   ```bash
   touch backend/data/quizzes/new-quiz.json
   ```

2. **Add quiz content:**
   ```json
   {
     "id": "new-quiz",
     "title": "New Quiz Title",
     "description": "Description",
     "questions": [...]
   }
   ```

3. **Validate with Zod:**
   ```typescript
   const quiz = QuizSchema.parse(data)  // Throws if invalid
   ```

4. **Test:**
   ```bash
   curl http://localhost:3001/api/quizzes/new-quiz
   ```

### Caching Quiz Data (Optional)

```typescript
// Simple in-memory cache
let quizCache: Quiz[] | null = null
let cacheTimestamp = 0
const CACHE_TTL = 60000  // 1 minute

export async function getAllQuizzes(): Promise<Quiz[]> {
  const now = Date.now()
  
  if (quizCache && (now - cacheTimestamp) < CACHE_TTL) {
    return quizCache
  }
  
  // Load from files
  const quizzes = await loadQuizzesFromFiles()
  quizCache = quizzes
  cacheTimestamp = now
  
  return quizzes
}
```

### CORS for Next.js Frontend

```typescript
import cors from 'cors'

app.use(cors({
  origin: process.env.FRONTEND_URL || 'http://localhost:3000',
  credentials: true,
}))
```

## Best Practices

### ALWAYS:
- Validate all quiz data with Zod schemas
- Return 404 for non-existent quiz IDs
- Use async/await with asyncHandler
- Separate routes, controllers, and services
- Store quizzes as JSON files in `data/quizzes/`
- Return quiz summaries (no questions) in list endpoint
- Return full quiz (with questions) in detail endpoint

### NEVER:
- Return quiz answers in list endpoint
- Store quiz data in database (use JSON files)
- Skip Zod validation for quiz data
- Mix business logic in route handlers
- Return internal error details to client
- Modify quiz JSON files at runtime

## Project-Specific Rules

### Quiz ID Format
- Use kebab-case: `agent-fundamentals`, `prompt-engineering`
- Match filename: `agent-fundamentals.json`
- Use as route param: `/api/quizzes/agent-fundamentals`

### No User State in Backend
- Backend serves quiz data only
- User progress stored in frontend localStorage
- No sessions, no authentication (username-only client-side)
- No database needed

### API Versioning (Future)
```typescript
// If needed later
router.use('/v1', routesV1)
router.use('/v2', routesV2)
```

## Resources

- **Express Skill:** [/skills/express/SKILL.md](/skills/express/SKILL.md)
- **QuizApp Domain:** [/skills/quizapp-domain/SKILL.md](/skills/quizapp-domain/SKILL.md)
- **QuizApp Testing:** [/skills/quizapp-testing/SKILL.md](/skills/quizapp-testing/SKILL.md)
- **Zod Documentation:** https://zod.dev

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtaborda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
