---
name: quizapp-testing
description: > Use when this capability is needed.
metadata:
  author: dtaborda
---

# QuizApp Testing Skill

## When to Use

Use this skill when:
- Writing unit tests for pure functions (score calculation, sorting, etc.)
- Writing integration tests for business logic
- Testing Express API endpoints with Supertest
- Testing React components (if needed)
- Validating Zod schemas against test data
- Setting up test suites for new features

## Critical Patterns

### Test Organization

**ALWAYS use this structure:**

```
package/
├── src/
│   ├── utils/
│   │   ├── score-calculator.ts
│   │   └── score-calculator.spec.ts    # Co-located
│   ├── routes/
│   │   ├── quiz.ts
│   │   └── quiz.spec.ts                # Co-located
│   └── app.ts
└── package.json
```

**Why co-locate:**
- Tests live next to implementation
- Easy to find and update
- Clear what's tested vs untested

### Test File Naming

| Type | Pattern | Example |
|------|---------|---------|
| Unit test | `*.spec.ts` | `score-calculator.spec.ts` |
| Integration test | `*.spec.ts` | `attempt-manager.spec.ts` |
| API test | `*.spec.ts` | `quiz-routes.spec.ts` |

**NEVER use `.test.ts`** (use `.spec.ts` for consistency)

### Vitest Configuration

**File: `vitest.config.ts`**

```typescript
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    globals: true,
    environment: 'node',  // or 'jsdom' for React components
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'dist/',
        '**/*.spec.ts',
        '**/*.config.ts',
      ],
    },
  },
})
```

### Test Structure (AAA Pattern)

**ALWAYS follow Arrange-Act-Assert:**

```typescript
import { describe, it, expect, beforeEach } from 'vitest'

describe('calculateScore', () => {
  it('should calculate correct score for all correct answers', () => {
    // Arrange
    const answers = [
      { questionId: '1', selectedOption: 'A', isCorrect: true },
      { questionId: '2', selectedOption: 'B', isCorrect: true },
    ]
    
    // Act
    const score = calculateScore(answers)
    
    // Assert
    expect(score).toBe(2)
  })
})
```

**Why AAA:**
- Clear test intent
- Easy to understand what's being tested
- Readable for AI agents and humans

## Unit Testing Patterns

### Pure Function Testing

```typescript
// src/utils/score-calculator.ts
export function calculateScore(answers: Answer[]): number {
  return answers.filter(a => a.isCorrect).length
}

// src/utils/score-calculator.spec.ts
import { describe, it, expect } from 'vitest'
import { calculateScore } from './score-calculator'

describe('calculateScore', () => {
  it('should return 0 for empty answers', () => {
    expect(calculateScore([])).toBe(0)
  })
  
  it('should count only correct answers', () => {
    const answers = [
      { questionId: '1', selectedOption: 'A', isCorrect: true },
      { questionId: '2', selectedOption: 'B', isCorrect: false },
      { questionId: '3', selectedOption: 'C', isCorrect: true },
    ]
    
    expect(calculateScore(answers)).toBe(2)
  })
  
  it('should return total count for all correct', () => {
    const answers = [
      { questionId: '1', selectedOption: 'A', isCorrect: true },
      { questionId: '2', selectedOption: 'B', isCorrect: true },
    ]
    
    expect(calculateScore(answers)).toBe(2)
  })
})
```

### Array/Sorting Testing

```typescript
// src/utils/leaderboard.ts
export function sortLeaderboard(attempts: Attempt[]): LeaderboardEntry[] {
  return attempts
    .map(a => ({
      ...a,
      percentage: (a.score! / a.answers.length) * 100
    }))
    .sort((a, b) => {
      if (b.percentage !== a.percentage) {
        return b.percentage - a.percentage  // DESC
      }
      return new Date(a.completedAt!).getTime() - new Date(b.completedAt!).getTime()  // ASC
    })
}

// src/utils/leaderboard.spec.ts
import { describe, it, expect } from 'vitest'
import { sortLeaderboard } from './leaderboard'

describe('sortLeaderboard', () => {
  it('should sort by score descending', () => {
    const attempts = [
      { score: 3, answers: [1,2,3,4,5], completedAt: '2024-01-01' },
      { score: 5, answers: [1,2,3,4,5], completedAt: '2024-01-02' },
      { score: 4, answers: [1,2,3,4,5], completedAt: '2024-01-03' },
    ]
    
    const result = sortLeaderboard(attempts)
    
    expect(result[0].score).toBe(5)  // Highest first
    expect(result[1].score).toBe(4)
    expect(result[2].score).toBe(3)
  })
  
  it('should use date as tie-breaker (earlier first)', () => {
    const attempts = [
      { score: 4, answers: [1,2,3,4,5], completedAt: '2024-01-03' },
      { score: 4, answers: [1,2,3,4,5], completedAt: '2024-01-01' },
      { score: 4, answers: [1,2,3,4,5], completedAt: '2024-01-02' },
    ]
    
    const result = sortLeaderboard(attempts)
    
    expect(result[0].completedAt).toBe('2024-01-01')  // Earlier first
    expect(result[1].completedAt).toBe('2024-01-02')
    expect(result[2].completedAt).toBe('2024-01-03')
  })
})
```

### Zod Schema Validation Testing

```typescript
// shared/schemas/quiz.ts
import { z } from 'zod'

export const QuizSchema = z.object({
  id: z.string(),
  title: z.string(),
  description: z.string(),
  questions: z.array(QuestionSchema),
})

// shared/schemas/quiz.spec.ts
import { describe, it, expect } from 'vitest'
import { QuizSchema } from './quiz'

describe('QuizSchema', () => {
  it('should validate valid quiz', () => {
    const validQuiz = {
      id: '1',
      title: 'Test Quiz',
      description: 'A test quiz',
      questions: [
        {
          id: 'q1',
          text: 'Question?',
          options: ['A', 'B', 'C', 'D'],
          correctOption: 'A',
          explanation: 'Explanation',
        }
      ],
    }
    
    expect(() => QuizSchema.parse(validQuiz)).not.toThrow()
  })
  
  it('should reject quiz without title', () => {
    const invalidQuiz = {
      id: '1',
      description: 'A test quiz',
      questions: [],
    }
    
    expect(() => QuizSchema.parse(invalidQuiz)).toThrow()
  })
})
```

## API Testing with Supertest

### Express Route Testing

```typescript
// backend/src/routes/quiz.ts
import { Router } from 'express'

export const quizRouter = Router()

quizRouter.get('/quizzes', (req, res) => {
  const quizzes = loadQuizzes()
  res.json(quizzes)
})

quizRouter.get('/quizzes/:id', (req, res) => {
  const quiz = loadQuiz(req.params.id)
  if (!quiz) {
    return res.status(404).json({ error: 'Quiz not found' })
  }
  res.json(quiz)
})

// backend/src/routes/quiz.spec.ts
import { describe, it, expect, beforeAll, afterAll } from 'vitest'
import request from 'supertest'
import { app } from '../app'

describe('GET /api/quizzes', () => {
  it('should return 200 and array of quizzes', async () => {
    const response = await request(app)
      .get('/api/quizzes')
      .expect(200)
      .expect('Content-Type', /json/)
    
    expect(response.body).toBeInstanceOf(Array)
    expect(response.body.length).toBeGreaterThan(0)
    expect(response.body[0]).toHaveProperty('id')
    expect(response.body[0]).toHaveProperty('title')
  })
})

describe('GET /api/quizzes/:id', () => {
  it('should return 200 and quiz for valid ID', async () => {
    const response = await request(app)
      .get('/api/quizzes/agent-fundamentals')
      .expect(200)
    
    expect(response.body).toHaveProperty('id', 'agent-fundamentals')
    expect(response.body).toHaveProperty('questions')
    expect(response.body.questions).toBeInstanceOf(Array)
  })
  
  it('should return 404 for non-existent quiz', async () => {
    const response = await request(app)
      .get('/api/quizzes/non-existent')
      .expect(404)
    
    expect(response.body).toHaveProperty('error')
  })
})

describe('GET /api/health', () => {
  it('should return 200 and status ok', async () => {
    const response = await request(app)
      .get('/api/health')
      .expect(200)
    
    expect(response.body).toEqual({ status: 'ok' })
  })
})
```

### Testing with Setup/Teardown

```typescript
import { describe, it, expect, beforeEach, afterEach } from 'vitest'
import request from 'supertest'
import { app } from '../app'

describe('Quiz API with state', () => {
  let testQuizId: string
  
  beforeEach(() => {
    // Setup: create test data
    testQuizId = 'test-quiz'
    createTestQuiz(testQuizId)
  })
  
  afterEach(() => {
    // Teardown: clean test data
    deleteTestQuiz(testQuizId)
  })
  
  it('should fetch created quiz', async () => {
    const response = await request(app)
      .get(`/api/quizzes/${testQuizId}`)
      .expect(200)
    
    expect(response.body.id).toBe(testQuizId)
  })
})
```

## Integration Testing Patterns

### Testing Attempt Lifecycle

```typescript
// frontend/lib/attempt-manager.spec.ts
import { describe, it, expect, beforeEach, afterEach } from 'vitest'
import { 
  startQuiz, 
  resumeQuiz, 
  completeAttempt,
  getActiveAttempts,
  getAttemptHistory,
} from './attempt-manager'

describe('Attempt Manager', () => {
  beforeEach(() => {
    localStorage.clear()
  })
  
  afterEach(() => {
    localStorage.clear()
  })
  
  it('should create new active attempt', () => {
    const quiz = { id: 'q1', title: 'Test', questions: [...] }
    const session = { username: 'test', loginTime: '2024-01-01' }
    
    const attempt = startQuiz(quiz, session, 'normal')
    
    expect(attempt.quizId).toBe('q1')
    expect(attempt.userId).toBe('test')
    expect(attempt.status).toBe('active')
    expect(attempt.questionOrder).toHaveLength(quiz.questions.length)
  })
  
  it('should resume existing attempt', () => {
    const quiz = { id: 'q1', title: 'Test', questions: [...] }
    const session = { username: 'test', loginTime: '2024-01-01' }
    
    const attempt1 = startQuiz(quiz, session, 'normal')
    const attempt2 = resumeQuiz('q1')
    
    expect(attempt2.id).toBe(attempt1.id)
    expect(attempt2.questionOrder).toEqual(attempt1.questionOrder)
  })
  
  it('should move attempt to history on completion', () => {
    const quiz = { id: 'q1', title: 'Test', questions: [...] }
    const session = { username: 'test', loginTime: '2024-01-01' }
    
    const attempt = startQuiz(quiz, session, 'normal')
    const completed = completeAttempt(attempt)
    
    expect(completed.status).toBe('completed')
    expect(completed.completedAt).toBeDefined()
    expect(completed.score).toBeDefined()
    
    const activeAttempts = getActiveAttempts()
    expect(activeAttempts['q1']).toBeUndefined()
    
    const history = getAttemptHistory()
    expect(history).toContainEqual(completed)
  })
  
  it('should maintain stable question order after refresh', () => {
    const quiz = { id: 'q1', title: 'Test', questions: [...] }
    const session = { username: 'test', loginTime: '2024-01-01' }
    
    const attempt1 = startQuiz(quiz, session, 'normal')
    const order1 = attempt1.questionOrder
    
    // Simulate page refresh (localStorage persists)
    const attempt2 = resumeQuiz('q1')
    const order2 = attempt2.questionOrder
    
    expect(order2).toEqual(order1)
  })
})
```

## Testing Best Practices

### Test Naming

**ALWAYS use descriptive test names:**

```typescript
// ✅ Good
it('should return 404 when quiz does not exist', () => {})
it('should calculate score as 0 for empty answers', () => {})
it('should randomize question order per attempt', () => {})

// ❌ Bad
it('works', () => {})
it('test 1', () => {})
it('returns data', () => {})
```

### Test Coverage

**ALWAYS test:**
- Happy path (valid input → expected output)
- Edge cases (empty arrays, null, undefined)
- Error cases (invalid input → error)
- Boundary conditions (min, max values)

**Example:**
```typescript
describe('calculateScore', () => {
  it('should handle empty answers', () => {})           // Edge case
  it('should calculate score for all correct', () => {})  // Happy path
  it('should calculate score for all incorrect', () => {}) // Edge case
  it('should calculate score for mixed results', () => {}) // Happy path
})
```

### Mocking

**Use mocking sparingly:**

```typescript
import { vi } from 'vitest'

// Mock external API
vi.mock('./api', () => ({
  fetchQuiz: vi.fn().mockResolvedValue({ id: '1', title: 'Test' })
}))

// Use in test
it('should load quiz from API', async () => {
  const quiz = await loadQuiz('1')
  expect(quiz.title).toBe('Test')
})
```

**AVOID mocking:**
- Internal functions (test real implementation)
- localStorage (use real localStorage in tests)
- Simple utilities (test actual behavior)

## Commands

```bash
# Run all tests
pnpm test

# Run tests in watch mode
pnpm test -- --watch

# Run specific test file
pnpm test -- src/utils/score-calculator.spec.ts

# Run tests matching pattern
pnpm test -- -t "calculateScore"

# Generate coverage report
pnpm test -- --coverage

# Run tests in specific package
pnpm --filter frontend test
pnpm --filter backend test
```

## Definition of Done (Testing)

Before marking feature complete:

- [ ] Unit tests for all pure functions
- [ ] Integration tests for business logic
- [ ] API tests for all endpoints (200, 404, 400 cases)
- [ ] Edge cases covered (empty, null, invalid input)
- [ ] All tests pass (`pnpm test`)
- [ ] Coverage meets threshold (aim for 80%+)
- [ ] No skipped tests (`it.skip`) without reason
- [ ] Test names are descriptive

## Common Pitfalls

### ❌ Testing Implementation Details

```typescript
// ❌ Bad - testing internal state
it('should set internal cache', () => {
  const manager = new AttemptManager()
  manager.start(quiz)
  expect(manager._cache).toBeDefined()  // Internal detail
})

// ✅ Good - testing behavior
it('should resume attempt after creation', () => {
  const manager = new AttemptManager()
  const attempt1 = manager.start(quiz)
  const attempt2 = manager.resume(quiz.id)
  expect(attempt2.id).toBe(attempt1.id)
})
```

### ❌ Not Cleaning Up After Tests

```typescript
// ❌ Bad - state leaks between tests
it('test 1', () => {
  localStorage.setItem('key', 'value')
  // No cleanup
})

// ✅ Good - clean state
afterEach(() => {
  localStorage.clear()
})
```

### ❌ Overly Complex Tests

```typescript
// ❌ Bad - testing too many things
it('should handle quiz flow', () => {
  const attempt = startQuiz()
  answerQuestion(attempt, 0, 'A')
  answerQuestion(attempt, 1, 'B')
  const completed = completeAttempt(attempt)
  expect(completed.score).toBe(2)
  expect(getHistory()).toContain(completed)
})

// ✅ Good - one thing per test
it('should create attempt on start', () => {})
it('should record answers', () => {})
it('should complete attempt', () => {})
it('should add completed attempt to history', () => {})
```

## Resources

- **Vitest docs:** https://vitest.dev
- **Supertest docs:** https://github.com/ladjs/supertest
- **Testing patterns:** See [/docs/architecture.md](/docs/architecture.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtaborda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
