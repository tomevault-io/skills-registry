---
name: typescript-patterns
description: Use when writing or refactoring TypeScript code. Covers type narrowing, generics, utility types, discriminated unions, type guards, branded types, and strict mode patterns.
metadata:
  author: canivel
---

# TypeScript Best Practices

## Type Narrowing

```ts
// Use typeof for primitives
function format(value: string | number): string {
  if (typeof value === 'number') return value.toFixed(2);
  return value.trim();
}

// Use 'in' for objects with different shapes
function getArea(shape: Circle | Rectangle): number {
  if ('radius' in shape) return Math.PI * shape.radius ** 2;
  return shape.width * shape.height;
}

// Use instanceof for class instances
function handleError(err: unknown) {
  if (err instanceof AppError) return err.statusCode;
  if (err instanceof Error) return 500;
  return 500;
}
```

## Discriminated Unions

```ts
// Always use a literal 'type' or 'kind' discriminant
type ApiResult<T> =
  | { status: 'success'; data: T }
  | { status: 'error'; error: string; code: number }
  | { status: 'loading' };

function renderResult(result: ApiResult<User>) {
  switch (result.status) {
    case 'success': return <UserCard user={result.data} />;
    case 'error': return <ErrorMessage message={result.error} />;
    case 'loading': return <Spinner />;
  }
}

// Use exhaustive checking
function assertNever(x: never): never {
  throw new Error(`Unexpected value: ${x}`);
}
```

## Generics

```ts
// Constrain generics with extends
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Generic with defaults
interface PaginatedResponse<T = unknown> {
  data: T[];
  meta: { page: number; limit: number; total: number };
}

// Generic factory function
function createStore<T>(initialState: T) {
  let state = initialState;
  return {
    getState: (): T => state,
    setState: (newState: Partial<T>) => { state = { ...state, ...newState }; },
  };
}

// NEVER use more than 2-3 generic parameters. If you need more, refactor.
```

## Utility Types

```ts
// Pick / Omit for partial types
type UserPreview = Pick<User, 'id' | 'name' | 'avatar'>;
type CreateUser = Omit<User, 'id' | 'createdAt' | 'updatedAt'>;

// Partial / Required
type UserUpdate = Partial<Pick<User, 'name' | 'email' | 'avatar'>>;

// Record for dictionaries
type StatusCounts = Record<TaskStatus, number>;

// Extract / Exclude for union manipulation
type SuccessResult = Extract<ApiResult, { status: 'success' }>;
type NonLoadingResult = Exclude<ApiResult, { status: 'loading' }>;

// ReturnType / Parameters for function types
type FetchReturn = ReturnType<typeof fetchUsers>;
type FetchArgs = Parameters<typeof fetchUsers>;
```

## Type Guards

```ts
// Custom type guard functions
function isUser(value: unknown): value is User {
  return (
    typeof value === 'object' &&
    value !== null &&
    'id' in value &&
    'email' in value &&
    typeof (value as User).email === 'string'
  );
}

// Type guard with assertion
function assertDefined<T>(value: T | undefined | null, name: string): asserts value is T {
  if (value == null) throw new Error(`Expected ${name} to be defined`);
}

// Usage
const user = getUser(id);
assertDefined(user, 'user');
// user is now narrowed to non-null User
```

## Branded Types

```ts
// Prevent mixing up primitive types that represent different things
type UserId = string & { readonly __brand: 'UserId' };
type ProjectId = string & { readonly __brand: 'ProjectId' };

function createUserId(id: string): UserId { return id as UserId; }
function createProjectId(id: string): ProjectId { return id as ProjectId; }

function getUser(id: UserId): User { ... }
function getProject(id: ProjectId): Project { ... }

const userId = createUserId('abc');
const projectId = createProjectId('xyz');

getUser(userId);      // OK
getUser(projectId);   // Compile error - prevents accidental misuse
```

## Module Patterns

```ts
// Re-export from barrel files sparingly. Only for public API surfaces.
// src/db/schema/index.ts
export { users, type User } from './users';
export { projects, type Project } from './projects';

// Prefer explicit imports in application code
import { users } from '@/db/schema/users';  // GOOD: clear origin
import { users } from '@/db/schema';        // OK for library-style modules

// Use 'satisfies' for type-safe object literals
const config = {
  port: 3000,
  host: 'localhost',
  debug: true,
} satisfies ServerConfig;
// config retains literal types while being validated against ServerConfig
```

## Strict Mode Patterns

```ts
// tsconfig.json: always enable strict mode
{
  "compilerOptions": {
    "strict": true,               // enables all strict checks
    "noUncheckedIndexedAccess": true,  // arrays/records may return undefined
    "exactOptionalPropertyTypes": true
  }
}

// Handle noUncheckedIndexedAccess
const items: string[] = ['a', 'b', 'c'];
const first = items[0]; // string | undefined
if (first) {
  console.log(first.toUpperCase()); // safe
}

// Use Map instead of Record when keys are dynamic
const cache = new Map<string, User>();
const user = cache.get(id); // string | undefined (naturally safe)
```

## Anti-Patterns

- NEVER use `any`. Use `unknown` and narrow, or define a proper type.
- NEVER use `as` type assertions to silence errors. Fix the type mismatch at its source.
- NEVER use `!` (non-null assertion) unless you can guarantee the value exists.
- NEVER use `@ts-ignore`. Use `@ts-expect-error` with a comment if suppression is truly needed.
- NEVER use `enum`. Use `as const` objects or union types instead.
- NEVER export types that are only used internally within a module.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
