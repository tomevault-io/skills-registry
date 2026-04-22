---
name: testing-qa
description: Complete testing and quality assurance guide for Next.js applications including unit tests, integration tests, E2E tests, test patterns, and quality workflows. Use when writing tests, ensuring code quality, implementing test coverage, or setting up testing infrastructure. Use when this capability is needed.
metadata:
  author: barisariburnu
---

# Testing & QA Skill

**Skill Location**: `{project_path}/skills/testing-qa/`

Comprehensive guide for testing Next.js applications, optimized for minimal token usage while maintaining test quality and coverage.

---

## When to Use This Skill (Trigger Patterns)

**MUST apply this skill when:**
- Writing unit tests for components and functions
- Creating integration tests for API routes
- Building E2E tests for user flows
- Setting up test infrastructure
- Ensuring code quality and test coverage
- Debugging test failures

**Trigger phrases:**
- "write tests for..."
- "create test coverage"
- "test this component/route"
- "add E2E tests"
- "ensure quality assurance"
- "debug test failure"

---

## Testing Stack

```
Testing Framework: Jest/Vitest
React Testing: @testing-library/react
E2E Testing: Playwright/Cypress
Coverage: Istanbul/NYC
Mocking: vi (Vitest) / jest.fn
```

---

## Token-Saving Strategies

### 1. Use Standard Test Patterns Without Explanation

**❌ INEFFICIENT:**
```
"This test verifies that the component renders correctly..."
```

**✅ EFFICIENT:**
```
// Component render test
```

### 2. Minimal Comments in Tests

**❌ INEFFICIENT:**
```typescript
// Arrange - Set up the component with props
const props = { title: 'Test' }
// Act - Render the component
const { getByText } = render(<Button {...props} />)
// Assert - Verify the title is rendered
expect(getByText('Test')).toBeInTheDocument()
```

**✅ EFFICIENT:**
```typescript
const props = { title: 'Test' }
render(<Button {...props} />)
expect(screen.getByText('Test')).toBeInTheDocument()
```

### 3. Reuse Test Utilities

```typescript
// src/lib/test-utils.ts
export const renderWithProviders = (ui: React.ReactElement) => {
  return render(ui, { wrapper: Providers })
}

// Usage in tests
renderWithProviders(<UserList />)
```

---

## Test Structure

```
src/
├── __tests__/
│   ├── unit/
│   │   ├── utils.test.ts
│   │   └── validators.test.ts
│   ├── components/
│   │   ├── Button.test.tsx
│   │   └── UserList.test.tsx
│   └── integration/
│       └── users.test.ts
└── e2e/
    ├── auth.spec.ts
    └── users.spec.ts
```

---

## Unit Tests

### 1. Utility Function Tests

```typescript
// src/__tests__/utils.test.ts
import { formatDate, calculateAge } from '@/lib/utils'

describe('formatDate', () => {
  it('formats date correctly', () => {
    const date = new Date('2024-01-15')
    expect(formatDate(date)).toBe('2024-01-15')
  })

  it('handles null input', () => {
    expect(formatDate(null)).toBe('')
  })
})

describe('calculateAge', () => {
  it('calculates age correctly', () => {
    const birthDate = new Date('1990-01-01')
    const age = calculateAge(birthDate)
    expect(age).toBeGreaterThanOrEqual(34)
  })

  it('returns null for null input', () => {
    expect(calculateAge(null)).toBeNull()
  })
})
```

### 2. Component Tests

```typescript
// src/__tests__/components/Button.test.tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { Button } from '@/components/ui/button'

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByText('Click me')).toBeInTheDocument()
  })

  it('calls onClick when clicked', async () => {
    const user = userEvent.setup()
    const handleClick = vi.fn()

    render(<Button onClick={handleClick}>Click me</Button>)
    await user.click(screen.getByRole('button', { name: 'Click me' }))

    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
  })

  it('shows loading state when loading', () => {
    render(<Button loading>Click me</Button>)
    expect(screen.getByText('Loading...')).toBeInTheDocument()
  })
})
```

### 3. Form Tests

```typescript
// src/__tests__/components/UserForm.test.tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { UserForm } from '@/components/features/user/UserForm'

describe('UserForm', () => {
  it('validates required fields', async () => {
    const user = userEvent.setup()
    render(<UserForm />)

    await user.click(screen.getByRole('button', { name: 'Submit' }))

    expect(screen.getByText('Name is required')).toBeInTheDocument()
    expect(screen.getByText('Email is required')).toBeInTheDocument()
  })

  it('submits valid form data', async () => {
    const user = userEvent.setup()
    const onSubmit = vi.fn()
    render(<UserForm onSubmit={onSubmit} />)

    await user.type(screen.getByLabelText('Name'), 'John Doe')
    await user.type(screen.getByLabelText('Email'), 'john@example.com')
    await user.click(screen.getByRole('button', { name: 'Submit' }))

    expect(onSubmit).toHaveBeenCalledWith({
      name: 'John Doe',
      email: 'john@example.com'
    })
  })
})
```

---

## Integration Tests

### 1. API Route Tests

```typescript
// src/__tests__/integration/users.test.ts
import { POST, GET } from '@/app/api/users/route'
import { NextRequest } from 'next/server'

vi.mock('@/lib/db', () => ({
  db: {
    user: {
      create: vi.fn(),
      findMany: vi.fn(),
      findUnique: vi.fn(),
      update: vi.fn(),
      delete: vi.fn()
    }
  }
}))

describe('POST /api/users', () => {
  it('creates user successfully', async () => {
    const userData = {
      name: 'John Doe',
      email: 'john@example.com'
    }

    const req = new NextRequest('http://localhost/api/users', {
      method: 'POST',
      body: JSON.stringify(userData)
    })

    const res = await POST(req)
    const data = await res.json()

    expect(res.status).toBe(201)
    expect(data.success).toBe(true)
  })

  it('returns validation error for invalid email', async () => {
    const userData = {
      name: 'John Doe',
      email: 'invalid-email'
    }

    const req = new NextRequest('http://localhost/api/users', {
      method: 'POST',
      body: JSON.stringify(userData)
    })

    const res = await POST(req)
    const data = await res.json()

    expect(res.status).toBe(400)
    expect(data.success).toBe(false)
  })
})

describe('GET /api/users', () => {
  it('returns list of users', async () => {
    const req = new NextRequest('http://localhost/api/users')
    const res = await GET(req)
    const data = await res.json()

    expect(res.status).toBe(200)
    expect(data.success).toBe(true)
    expect(Array.isArray(data.data.users)).toBe(true)
  })

  it('paginates results', async () => {
    const req = new NextRequest('http://localhost/api/users?page=2&limit=5')
    const res = await GET(req)
    const data = await res.json()

    expect(data.data.pagination.page).toBe(2)
    expect(data.data.pagination.limit).toBe(5)
  })
})
```

---

## E2E Tests (Playwright)

### 1. Basic E2E Test

```typescript
// e2e/auth.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Authentication', () => {
  test('allows user to login', async ({ page }) => {
    await page.goto('/login')

    await page.fill('input[name="email"]', 'test@example.com')
    await page.fill('input[name="password"]', 'password123')
    await page.click('button[type="submit"]')

    await expect(page).toHaveURL('/dashboard')
    await expect(page.locator('h1')).toContainText('Welcome')
  })

  test('shows error for invalid credentials', async ({ page }) => {
    await page.goto('/login')

    await page.fill('input[name="email"]', 'test@example.com')
    await page.fill('input[name="password"]', 'wrongpassword')
    await page.click('button[type="submit"]')

    await expect(page.locator('.error')).toContainText('Invalid credentials')
  })
})
```

### 2. CRUD Flow Test

```typescript
// e2e/users.spec.ts
import { test, expect } from '@playwright/test'

test.describe('User Management', () => {
  test.beforeEach(async ({ page }) => {
    // Login before each test
    await page.goto('/login')
    await page.fill('input[name="email"]', 'admin@example.com')
    await page.fill('input[name="password"]', 'admin123')
    await page.click('button[type="submit"]')
    await expect(page).toHaveURL('/dashboard/users')
  })

  test('creates new user', async ({ page }) => {
    await page.click('button:has-text("Add User")')

    await page.fill('input[name="name"]', 'Jane Doe')
    await page.fill('input[name="email"]', 'jane@example.com')
    await page.click('button:has-text("Save")')

    await expect(page.locator('.success')).toContainText('User created')
    await expect(page.locator('table')).toContainText('Jane Doe')
  })

  test('edits existing user', async ({ page }) => {
    await page.click('tr:first-child button:has-text("Edit")')

    await page.fill('input[name="name"]', 'Updated Name')
    await page.click('button:has-text("Save")')

    await expect(page.locator('.success')).toContainText('User updated')
    await expect(page.locator('table')).toContainText('Updated Name')
  })

  test('deletes user', async ({ page }) => {
    await page.click('tr:first-child button:has-text("Delete")')
    await page.click('button:has-text("Confirm")')

    await expect(page.locator('.success')).toContainText('User deleted')
  })
})
```

---

## Test Patterns

### 1. Mocking Database

```typescript
import { db } from '@/lib/db'
import { beforeEach, vi } from 'vitest'

beforeEach(() => {
  vi.clearAllMocks()
})

test('fetches users from database', async () => {
  const mockUsers = [
    { id: '1', name: 'John', email: 'john@example.com' }
  ]

  vi.mocked(db.user.findMany).mockResolvedValue(mockUsers)

  const result = await getUsers()

  expect(result).toEqual(mockUsers)
  expect(db.user.findMany).toHaveBeenCalledTimes(1)
})
```

### 2. Mocking API Calls

```typescript
import { fetchUsers } from '@/lib/api'

test('fetches users from API', async () => {
  global.fetch = vi.fn(() =>
    Promise.resolve({
      ok: true,
      json: () => Promise.resolve({ users: [] })
    })
  ) as any

  const users = await fetchUsers()

  expect(users).toEqual([])
})
```

### 3. Testing Async Components

```typescript
test('shows loading state while fetching', async () => {
  vi.mock('@/lib/api', () => ({
    fetchUsers: vi.fn(() => new Promise(resolve => setTimeout(() => resolve([]), 100)))
  }))

  render(<UserList />)

  expect(screen.getByText('Loading...')).toBeInTheDocument()

  await waitFor(() => {
    expect(screen.queryByText('Loading...')).not.toBeInTheDocument()
  })
})
```

---

## Coverage Goals

```
Target Coverage:
- Statements: > 80%
- Branches: > 75%
- Functions: > 80%
- Lines: > 80%

Critical Areas: 100%
- Authentication/Authorization
- Payment/Financial operations
- Data validation
- API error handling
```

---

## Test Quality Checklist

### Before Writing Tests
- [ ] Identify what needs to be tested
- [ ] Determine test type (unit/integration/E2E)
- [ ] Set up necessary mocks
- [ ] Define expected behavior

### While Writing Tests
- [ ] Write descriptive test names
- [ ] Follow AAA pattern (Arrange, Act, Assert)
- [ ] Test edge cases and error states
- [ ] Keep tests focused and isolated

### After Writing Tests
- [ ] All tests pass
- [ ] Tests are fast and reliable
- [ ] Coverage meets requirements
- [ ] Tests document behavior

---

## Common Test Scenarios

### 1. Component Renders
```typescript
render(<Component />)
expect(screen.getByText('Content')).toBeInTheDocument()
```

### 2. User Interaction
```typescript
const user = userEvent.setup()
await user.click(screen.getByRole('button'))
expect(handleClick).toHaveBeenCalled()
```

### 3. Form Validation
```typescript
await user.click(submitButton)
expect(screen.getByText('Required')).toBeInTheDocument()
```

### 4. Async Operations
```typescript
await waitFor(() => {
  expect(screen.getByText('Loaded')).toBeInTheDocument()
})
```

### 5. API Response
```typescript
const res = await fetch('/api/data')
const data = await res.json()
expect(data.success).toBe(true)
```

---

## Token-Efficient Prompt Templates

### Write Tests
```
Write tests for <COMPONENT/FUNCTION>:
- Unit tests for core logic
- Integration tests for dependencies
- Test edge cases
- Mock external services
```

### Add Coverage
```
Add test coverage for <AREA>:
- Critical paths: 100%
- Error states covered
- Edge cases tested
```

### E2E Tests
```
Create E2E test for <FLOW>:
- Test complete user journey
- Include happy path
- Test error scenarios
```

---

## Quick Commands

```bash
# Run all tests
bun test

# Run tests in watch mode
bun test --watch

# Run specific test file
bun test UserList.test.tsx

# Run tests matching pattern
bun test -- Button

# Generate coverage report
bun test --coverage

# Run E2E tests
bun test:e2e

# Run specific E2E test
bun test:e2e auth.spec.ts
```

---

## Important Reminders

1. **Test what matters** - Focus on critical paths
2. **Keep tests simple** - Avoid complex logic in tests
3. **Use descriptive names** - Test names should document behavior
4. **Mock external dependencies** - Keep tests isolated
5. **Test edge cases** - Not just happy paths
6. **Maintain coverage** - But don't chase 100% blindly
7. **Keep tests fast** - Slow tests won't be run
8. **Fix failing tests immediately** - Don't accumulate tech debt

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/barisariburnu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
