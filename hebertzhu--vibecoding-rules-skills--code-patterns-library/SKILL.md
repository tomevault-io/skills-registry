---
name: code-patterns-library
description: Reusable code patterns and design templates following glue coding philosophy - prioritize searching, reusing, and connecting over creating. Includes API response formats, custom React hooks, Repository pattern, UI component patterns, and skeleton project strategies. Use when implementing new features, designing APIs, creating components, or when asked about code patterns, best practices, or reusable solutions. Use when this capability is needed.
metadata:
  author: hebertzhu
---

# Code Patterns Library

Proven, reusable code patterns following the glue coding philosophy.

## Core Philosophy

> **Reuse > Connect > Create**

Priority order when implementing features:
1. 🔍 **Search** - Find battle-tested solutions
2. 📦 **Reuse** - Use mature libraries and frameworks
3. 🔗 **Connect** - Write glue code to connect modules
4. ✏️ **Create** - Only write new code when necessary

## Code Source Priority

```
Official docs examples > Mature open-source > Existing project code > New code
```

## API Response Format

### Standard Interface

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
  meta?: {
    total: number
    page: number
    limit: number
  }
}
```

### Usage Examples

```typescript
// Success response
const successResponse: ApiResponse<User> = {
  success: true,
  data: {
    id: '123',
    name: 'John Doe',
    email: 'john@example.com'
  }
}

// Error response
const errorResponse: ApiResponse<never> = {
  success: false,
  error: 'User not found'
}

// Paginated response
const paginatedResponse: ApiResponse<User[]> = {
  success: true,
  data: [/* users */],
  meta: {
    total: 100,
    page: 1,
    limit: 10
  }
}
```

### Utility Functions

```typescript
// utils/api-response.ts
export function successResponse<T>(
  data: T, 
  meta?: ApiResponse<T>['meta']
): ApiResponse<T> {
  return { success: true, data, meta }
}

export function errorResponse(error: string): ApiResponse<never> {
  return { success: false, error }
}

// Usage
export async function getUser(id: string): Promise<ApiResponse<User>> {
  try {
    const user = await db.users.findById(id)
    if (!user) {
      return errorResponse('User not found')
    }
    return successResponse(user)
  } catch (error) {
    return errorResponse(error.message)
  }
}
```

## Custom React Hooks

### useDebounce

```typescript
import { useState, useEffect } from 'react'

export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value)
    }, delay)

    return () => clearTimeout(handler)
  }, [value, delay])

  return debouncedValue
}
```

**Use Case**: Search input, API call debouncing

```typescript
function SearchComponent() {
  const [searchTerm, setSearchTerm] = useState('')
  const debouncedSearchTerm = useDebounce(searchTerm, 500)

  useEffect(() => {
    if (debouncedSearchTerm) {
      searchAPI(debouncedSearchTerm)
    }
  }, [debouncedSearchTerm])

  return (
    <input
      value={searchTerm}
      onChange={(e) => setSearchTerm(e.target.value)}
      placeholder="Search..."
    />
  )
}
```

### useLocalStorage

```typescript
import { useState } from 'react'

export function useLocalStorage<T>(
  key: string,
  initialValue: T
): [T, (value: T) => void] {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch (error) {
      console.error(error)
      return initialValue
    }
  })

  const setValue = (value: T) => {
    try {
      setStoredValue(value)
      window.localStorage.setItem(key, JSON.stringify(value))
    } catch (error) {
      console.error(error)
    }
  }

  return [storedValue, setValue]
}
```

**Use Case**: Persist user preferences, theme settings

```typescript
function ThemeToggle() {
  const [theme, setTheme] = useLocalStorage('theme', 'light')

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Current theme: {theme}
    </button>
  )
}
```

### useAsync

```typescript
import { useState, useEffect, useCallback } from 'react'

interface AsyncState<T> {
  loading: boolean
  data: T | null
  error: Error | null
}

export function useAsync<T>(
  asyncFunction: () => Promise<T>,
  immediate = true
) {
  const [state, setState] = useState<AsyncState<T>>({
    loading: immediate,
    data: null,
    error: null
  })

  const execute = useCallback(async () => {
    setState({ loading: true, data: null, error: null })
    try {
      const data = await asyncFunction()
      setState({ loading: false, data, error: null })
    } catch (error) {
      setState({ loading: false, data: null, error: error as Error })
    }
  }, [asyncFunction])

  useEffect(() => {
    if (immediate) {
      execute()
    }
  }, [execute, immediate])

  return { ...state, execute }
}
```

**Use Case**: Async data fetching

```typescript
function UserProfile({ userId }: { userId: string }) {
  const { loading, data, error } = useAsync(
    () => fetchUser(userId),
    true
  )

  if (loading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>
  if (!data) return null

  return <div>{data.name}</div>
}
```

## Repository Pattern

### Interface

```typescript
interface Repository<T> {
  findAll(filters?: Filters): Promise<T[]>
  findById(id: string): Promise<T | null>
  create(data: CreateDto): Promise<T>
  update(id: string, data: UpdateDto): Promise<T>
  delete(id: string): Promise<void>
}

interface Filters {
  [key: string]: any
}

type CreateDto = Omit<T, 'id' | 'createdAt' | 'updatedAt'>
type UpdateDto = Partial<CreateDto>
```

### Implementation

```typescript
// repositories/UserRepository.ts
import { supabase } from '../lib/supabase'

export class UserRepository implements Repository<User> {
  private table = 'users'

  async findAll(filters?: Filters): Promise<User[]> {
    let query = supabase.from(this.table).select('*')
    
    if (filters) {
      Object.entries(filters).forEach(([key, value]) => {
        query = query.eq(key, value)
      })
    }
    
    const { data, error } = await query
    if (error) throw error
    return data || []
  }

  async findById(id: string): Promise<User | null> {
    const { data, error } = await supabase
      .from(this.table)
      .select('*')
      .eq('id', id)
      .single()
    
    if (error) return null
    return data
  }

  async create(data: CreateDto): Promise<User> {
    const { data: user, error } = await supabase
      .from(this.table)
      .insert(data)
      .select()
      .single()
    
    if (error) throw error
    return user
  }

  async update(id: string, data: UpdateDto): Promise<User> {
    const { data: user, error } = await supabase
      .from(this.table)
      .update(data)
      .eq('id', id)
      .select()
      .single()
    
    if (error) throw error
    return user
  }

  async delete(id: string): Promise<void> {
    const { error } = await supabase
      .from(this.table)
      .delete()
      .eq('id', id)
    
    if (error) throw error
  }
}
```

### Usage

```typescript
// services/UserService.ts
import { UserRepository } from '../repositories/UserRepository'

export class UserService {
  private userRepo = new UserRepository()

  async getUserById(id: string) {
    const user = await this.userRepo.findById(id)
    if (!user) {
      throw new Error('User not found')
    }
    return user
  }

  async createUser(data: CreateUserDto) {
    // Business logic validation
    if (!data.email.includes('@')) {
      throw new Error('Invalid email')
    }
    
    return await this.userRepo.create(data)
  }

  async getActiveUsers() {
    return await this.userRepo.findAll({ isActive: true })
  }
}
```

## UI Component Patterns

### Composition Pattern

```typescript
// components/Card/Card.tsx
interface CardProps {
  children: React.ReactNode
  className?: string
}

export function Card({ children, className }: CardProps) {
  return (
    <div className={`card ${className || ''}`}>
      {children}
    </div>
  )
}

// components/Card/CardHeader.tsx
export function CardHeader({ children }: { children: React.ReactNode }) {
  return <div className="card-header">{children}</div>
}

// components/Card/CardBody.tsx
export function CardBody({ children }: { children: React.ReactNode }) {
  return <div className="card-body">{children}</div>
}

// components/Card/CardFooter.tsx
export function CardFooter({ children }: { children: React.ReactNode }) {
  return <div className="card-footer">{children}</div>
}

// Usage
import { Card, CardHeader, CardBody, CardFooter } from './components/Card'

function UserCard() {
  return (
    <Card>
      <CardHeader>
        <h2>User Info</h2>
      </CardHeader>
      <CardBody>
        <p>Name: John Doe</p>
        <p>Email: john@example.com</p>
      </CardBody>
      <CardFooter>
        <button>Edit</button>
      </CardFooter>
    </Card>
  )
}
```

### Render Props Pattern

```typescript
interface DataFetcherProps<T> {
  url: string
  children: (data: T | null, loading: boolean, error: Error | null) => React.ReactNode
}

function DataFetcher<T>({ url, children }: DataFetcherProps<T>) {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [url])

  return <>{children(data, loading, error)}</>
}

// Usage
function UserList() {
  return (
    <DataFetcher<User[]> url="/api/users">
      {(users, loading, error) => {
        if (loading) return <div>Loading...</div>
        if (error) return <div>Error: {error.message}</div>
        if (!users) return null
        
        return (
          <ul>
            {users.map(user => (
              <li key={user.id}>{user.name}</li>
            ))}
          </ul>
        )
      }}
    </DataFetcher>
  )
}
```

## Skeleton Project Strategy

### When Implementing New Features

1. **Search for Skeleton Projects**

```bash
# GitHub search keywords
"react typescript starter"
"nextjs authentication boilerplate"
"express api template"
```

**Evaluation Criteria**:
- ⭐ Stars count (community approval)
- 📅 Recent updates (active maintenance)
- 📝 Documentation quality (usability)
- 🔒 Security (no known vulnerabilities)
- 📦 Dependency health (no outdated deps)

2. **Parallel Evaluation**

Evaluate candidates across dimensions:
- **Security**: Check dependency vulnerabilities, review auth implementation
- **Scalability**: Clear architecture, easy to customize, plugin support
- **Relevance**: Tech stack match, feature coverage, learning curve
- **Implementation**: Integration difficulty, migration cost, maintenance burden

3. **Clone Best Match**

```bash
git clone https://github.com/user/best-match-project.git
cd best-match-project
git checkout -b integrate-to-your-project

# Extract needed parts
# Remove unnecessary files
# Adjust configuration
```

4. **Iterate Within Verified Structure**

- Keep skeleton's core architecture
- Customize features as needed
- Add project-specific business logic
- Follow original project's coding style
- Preserve original test structure

### Recommended Skeletons

**React + TypeScript Components**:
- shadcn/ui - Copy-paste components
- Radix UI - Unstyled accessible components
- Headless UI - Tailwind official components

**API Backend Templates**:
- nestjs/nest - Enterprise Node.js framework
- express-typescript-boilerplate - Express + TS
- fastify-starter - High-performance API framework

**Full-Stack Templates**:
- t3-stack - Next.js + tRPC + Prisma
- create-t3-app - T3 Stack scaffolding
- supabase-starter - Supabase full-stack template

## Utility Functions

### Type-Safe Event Handling

```typescript
// utils/events.ts
export function createEventHandler<T extends Event>(
  handler: (event: T) => void
) {
  return (event: T) => {
    event.preventDefault()
    handler(event)
  }
}

// Usage
const handleSubmit = createEventHandler<FormEvent<HTMLFormElement>>((e) => {
  const formData = new FormData(e.currentTarget)
  // Process form data
})
```

### Safe Type Guards

```typescript
// utils/type-guards.ts
export function isString(value: unknown): value is string {
  return typeof value === 'string'
}

export function isNumber(value: unknown): value is number {
  return typeof value === 'number' && !isNaN(value)
}

export function isArray<T>(value: unknown): value is T[] {
  return Array.isArray(value)
}

// Usage
function processValue(value: unknown) {
  if (isString(value)) {
    return value.toUpperCase()
  }
  if (isNumber(value)) {
    return value * 2
  }
  throw new Error('Invalid value type')
}
```

## Best Practices Summary

1. **Prioritize Reuse** - Search for existing solutions, avoid reinventing the wheel
2. **Standardize Interfaces** - Use consistent API response formats and data structures
3. **Type Safety** - Leverage TypeScript's type system fully
4. **Component Thinking** - Break UI into reusable small components
5. **Separation of Concerns** - Repository pattern separates data access from business logic
6. **Skeleton Projects** - Start new features based on mature projects
7. **Continuous Learning** - Follow community best practices and new patterns

---

*Remember: Good code isn't written from scratch, it's built on the shoulders of giants by reusing and composing validated solutions.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hebertzhu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
