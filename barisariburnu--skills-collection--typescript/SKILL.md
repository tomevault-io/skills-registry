---
name: typescript
description: Complete TypeScript best practices guide including type definitions, generics, utility types, patterns, type safety, and type inference strategies. Use when defining types, creating generic components, ensuring type safety, or implementing type patterns. Use when this capability is needed.
metadata:
  author: barisariburnu
---

# TypeScript Skill

**Skill Location**: `{project_path}/skills/typescript/`

Comprehensive guide for writing type-safe TypeScript code, optimized for minimal token usage while maintaining type safety and developer experience.

---

## When to Use This Skill (Trigger Patterns)

**MUST apply this skill when:**

- Defining TypeScript types and interfaces
- Creating generic components and functions
- Ensuring type safety
- Working with complex type transformations
- Using utility types
- Designing type-safe APIs
- Refactoring JavaScript to TypeScript

**Trigger phrases:**

- "define types for..."
- "make this type-safe"
- "create generic..."
- "add TypeScript to..."
- "fix type errors"
- "improve type definitions"

---

## Core Principles

### 1. Type Over Any

Avoid `any` - use `unknown` or specific types

### 2. Type Inference

Let TypeScript infer types when possible

### 3. Strict Mode

Always use strict TypeScript settings

### 4. No Type Assertions

Prefer type guards over type assertions

---

## Token-Saving Strategies

### 1. Use Self-Documenting Types

**❌ INEFFICIENT:**

```typescript
// This interface represents a user object with their basic information
interface User {
  // The unique identifier for the user
  id: string;
  // The user's full name
  name: string;
}
```

**✅ EFFICIENT:**

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  createdAt: Date;
}
```

### 2. Assume TypeScript Knowledge

Don't explain:

- What `interface` vs `type` does
- Basic generic syntax
- How type inference works

Focus on:

- Best practices
- Patterns
- Advanced scenarios

### 3. Use Utility Types

**❌ INEFFICIENT:** Re-implement common patterns

**✅ EFFICIENT:** Use built-in utility types

```typescript
// Instead of manually creating partial type
interface UserUpdate {
  name?: string;
  email?: string;
  role?: string;
}

// Use Partial
type UserUpdate = Partial<User>;
```

---

## Type Definitions

### 1. Interface vs Type

```typescript
// Use interface for object shapes
interface User {
  id: string;
  name: string;
  email: string;
  createdAt: Date;
}

// Use type for unions, primitives, utility types
type ID = string | number;
type Status = "active" | "inactive" | "pending";
type UserWithPosts = User & { posts: Post[] };
```

### 2. Model Types

```typescript
// Base model
interface Model {
  id: string;
  createdAt: Date;
  updatedAt: Date;
}

// Domain models
interface User extends Model {
  name: string;
  email: string;
  role: UserRole;
}

interface Post extends Model {
  title: string;
  content: string;
  authorId: string;
  author: User;
  status: PostStatus;
}

// Enums as union types
type UserRole = "user" | "admin" | "moderator";
type PostStatus = "draft" | "published" | "archived";
```

### 3. API Types

```typescript
// Request/Response types
interface ApiResponse<T> {
  success: boolean;
  data: T;
  error?: ApiError;
}

interface ApiError {
  message: string;
  code?: string;
}

// API endpoints
interface UserCreateRequest {
  name: string;
  email: string;
  role?: UserRole;
}

interface UserResponse extends User {}

// Usage
const response: ApiResponse<UserResponse> = {
  success: true,
  data: user,
};
```

---

## Generics

### 1. Generic Functions

```typescript
// Generic function with constraints
function fetchData<T>(url: string): Promise<T> {
  return fetch(url).then((r) => r.json());
}

// Generic function with default type
function createResponse<T = any>(data: T): ApiResponse<T> {
  return { success: true, data };
}

// Generic with constraints
function ensureString<T extends string>(value: T): string {
  return value;
}

// Multiple generics
function zip<A, B>(a: A[], b: B[]): [A, B][] {
  return a.map((val, i) => [val, b[i]]);
}
```

### 2. Generic Components

```typescript
// Generic component props
interface TableProps<T> {
  data: T[]
  columns: ColumnDef<T>[]
  onRowClick?: (row: T) => void
}

export function Table<T>({ data, columns, onRowClick }: TableProps<T>) {
  return (
    <table>
      {data.map((row, i) => (
        <tr key={i} onClick={() => onRowClick?.(row)}>
          {columns.map((col, j) => (
            <td key={j}>{col.accessor(row)}</td>
          ))}
        </tr>
      ))}
    </table>
  )
}

// Usage
interface User {
  id: string
  name: string
  email: string
}

const columns: ColumnDef<User>[] = [
  { header: 'Name', accessor: (u) => u.name },
  { header: 'Email', accessor: (u) => u.email }
]

<Table data={users} columns={columns} />
```

### 3. Generic Hooks

```typescript
// Generic hook
function useQuery<T>(key: string[], queryFn: () => Promise<T>) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    queryFn()
      .then(setData)
      .finally(() => setLoading(false));
  }, [queryFn]);

  return { data, loading };
}

// Usage
interface User {
  id: string;
  name: string;
}

const { data: users, loading } = useQuery<User[]>(["users"], () =>
  fetch("/api/users").then((r) => r.json())
);
```

---

## Utility Types

### 1. Built-in Utility Types

```typescript
// Partial - Makes all properties optional
type UserUpdate = Partial<User>;

// Required - Makes all properties required
type RequiredUser = Required<PartialUser>;

// Readonly - Makes all properties readonly
type ReadonlyUser = Readonly<User>;

// Record - Creates object type
type UserMap = Record<string, User>;

// Pick - Pick specific properties
type UserName = Pick<User, "name" | "email">;

// Omit - Omit specific properties
type UserWithoutId = Omit<User, "id">;

// Exclude - Exclude from union
type Status = "active" | "inactive" | "pending";
type ActiveStatus = Exclude<Status, "inactive" | "pending">;

// Extract - Extract from union
type StringKeys<T> = Extract<keyof T, string>;

// ReturnType - Get return type
type GetUser = () => User;
type UserResult = ReturnType<GetUser>;

// Parameters - Get parameter types
type CreateUser = (user: UserCreate) => Promise<User>;
type CreateUserParams = Parameters<CreateUser>[0];
```

### 2. Custom Utility Types

```typescript
// Deep partial
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

// Required keys
type RequiredKeys<T> = {
  [K in keyof T]-?: T[K];
};

// Make specific keys required
type RequiredBy<T, K extends keyof T> = T & Required<Pick<T, K>>;

// Nullable
type Nullable<T> = T | null;

// Maybe
type Maybe<T> = T | null | undefined;

// ValueOf
type ValueOf<T> = T[keyof T];

// NonEmptyArray
type NonEmptyArray<T> = [T, ...T[]];
```

---

## Type Guards and Narrowing

### 1. Type Guards

```typescript
// typeof guard
function processValue(value: string | number) {
  if (typeof value === "string") {
    return value.toUpperCase(); // string
  }
  return value.toFixed(2); // number
}

// instanceof guard
class Dog {
  bark() {}
}
class Cat {
  meow() {}
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark();
  } else {
    animal.meow();
  }
}

// Custom type guard
function isUser(obj: any): obj is User {
  return "id" in obj && "name" in obj && "email" in obj;
}

// Usage
if (isUser(data)) {
  console.log(data.name); // Type is User
}
```

### 2. Discriminated Unions

```typescript
type Success<T> = {
  type: "success";
  data: T;
};

type Error = {
  type: "error";
  message: string;
};

type Result<T> = Success<T> | Error;

function handleResult<T>(result: Result<T>) {
  if (result.type === "success") {
    console.log(result.data); // Type is T
  } else {
    console.log(result.message); // Type is string
  }
}
```

### 3. Type Predicates

```typescript
function isArray<T>(value: unknown): value is T[] {
  return Array.isArray(value);
}

function hasProperty<T extends object>(
  obj: T,
  key: PropertyKey
): key is keyof T {
  return key in obj;
}
```

---

## Type Inference

### 1. Let TypeScript Infer

```typescript
// Good - TypeScript infers return type
function getUser(id: string) {
  return db.user.findUnique({ where: { id } });
}

// Better - Explicit when needed
function getUser(id: string): Promise<User | null> {
  return db.user.findUnique({ where: { id } });
}

// Bad - Over-typed
function getUser(id: string): Promise<{
  id: string;
  name: string;
  email: string;
  createdAt: Date;
} | null> {
  return db.user.findUnique({ where: { id } });
}
```

### 2. Type Inference in Callbacks

```typescript
// TypeScript infers item type
users.filter((user) => user.isActive);

// Explicit when inference fails
users.filter((user: User) => user.isActive);
```

### 3. Generic Type Inference

```typescript
// Type inferred from arguments
const users = await fetchData<User[]>("/api/users");
const user = await fetchData<User>("/api/users/1");
```

---

## Common Patterns

### 1. Builder Pattern

```typescript
class QueryBuilder<T> {
  private filters: string[] = [];

  where(condition: string): this {
    this.filters.push(condition);
    return this;
  }

  build(): string {
    return this.filters.join(" AND ");
  }
}

const query = new QueryBuilder<User>()
  .where("isActive = true")
  .where("age > 18")
  .build();
```

### 2. Factory Pattern

```typescript
interface UserFactory {
  create(data: UserCreate): User;
}

class DefaultUserFactory implements UserFactory {
  create(data: UserCreate): User {
    return {
      id: generateId(),
      ...data,
      role: data.role || "user",
      createdAt: new Date(),
      updatedAt: new Date(),
    };
  }
}
```

### 3. Repository Pattern

```typescript
interface Repository<T> {
  findById(id: string): Promise<T | null>;
  findAll(): Promise<T[]>;
  create(data: Omit<T, "id">): Promise<T>;
  update(id: string, data: Partial<T>): Promise<T>;
  delete(id: string): Promise<void>;
}

class UserRepository implements Repository<User> {
  async findById(id: string): Promise<User | null> {
    return db.user.findUnique({ where: { id } });
  }

  async findAll(): Promise<User[]> {
    return db.user.findMany();
  }

  async create(data: Omit<User, "id">): Promise<User> {
    return db.user.create({ data });
  }

  async update(id: string, data: Partial<User>): Promise<User> {
    return db.user.update({ where: { id }, data });
  }

  async delete(id: string): Promise<void> {
    await db.user.delete({ where: { id } });
  }
}
```

### 4. Event Emitter

```typescript
type EventMap = {
  userCreated: User;
  userUpdated: User;
  userDeleted: { id: string };
};

class EventEmitter<T extends Record<string, any>> {
  private listeners: {
    [K in keyof T]?: ((data: T[K]) => void)[];
  } = {};

  on<K extends keyof T>(event: K, listener: (data: T[K]) => void): void {
    if (!this.listeners[event]) {
      this.listeners[event] = [];
    }
    this.listeners[event]!.push(listener);
  }

  emit<K extends keyof T>(event: K, data: T[K]): void {
    this.listeners[event]?.forEach((listener) => listener(data));
  }
}

// Usage
const emitter = new EventEmitter<EventMap>();
emitter.on("userCreated", (user) => {
  console.log(`User created: ${user.name}`);
});
emitter.emit("userCreated", user);
```

---

## Type-Safe API

### 1. Type-Safe API Client

```typescript
interface ApiEndpoints {
  "/api/users": {
    GET: { response: User[] };
    POST: { body: UserCreate; response: User };
  };
  "/api/users/[id]": {
    GET: { response: User };
    PUT: { body: Partial<User>; response: User };
    DELETE: { response: { success: boolean } };
  };
}

type ApiClient = {
  [K in keyof ApiEndpoints]: {
    [M in keyof ApiEndpoints[K]]: (
      ...args: ApiEndpoints[K][M] extends { body: infer B }
        ? [string | number, B]
        : [string | number]
    ) => Promise<ApiEndpoints[K][M] extends { response: infer R } ? R : never>;
  };
};

class Api implements ApiClient {
  async get(url: string): Promise<any> {
    return fetch(url).then((r) => r.json());
  }

  async post(url: string, body: any): Promise<any> {
    return fetch(url, {
      method: "POST",
      body: JSON.stringify(body),
    }).then((r) => r.json());
  }
}
```

### 2. Type-Safe React Hooks

```typescript
function useApi<T>(
  endpoint: string,
  options?: { method?: "GET" | "POST" | "PUT" | "DELETE"; body?: any }
) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    fetch(endpoint, {
      method: options?.method || "GET",
      headers: { "Content-Type": "application/json" },
      body: options?.body ? JSON.stringify(options.body) : undefined,
    })
      .then((r) => r.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [endpoint, options]);

  return { data, loading, error };
}

// Usage
const { data: users } = useApi<User[]>("/api/users");
```

---

## Common Pitfalls & Solutions

### ❌ Problem: Using `any` type

**✅ Solution:** Use `unknown` or specific types

```typescript
// Bad
function processData(data: any) {
  return data.name;
}

// Good
function processData(data: { name: string }) {
  return data.name;
}

// Better with generics
function processData<T extends { name: string }>(data: T): string {
  return data.name;
}
```

### ❌ Problem: Type assertions

**✅ Solution:** Use type guards

```typescript
// Bad
const user = data as User;

// Good
if (isUser(data)) {
  const user = data;
}
```

### ❌ Problem: Duplicate types

**✅ Solution:** Reuse and compose types

```typescript
// Bad
interface UserResponse {
  id: string;
  name: string;
  email: string;
}

interface UserCreate {
  id?: string;
  name: string;
  email: string;
}

// Good
interface User {
  id: string;
  name: string;
  email: string;
}

type UserCreate = Omit<User, "id">;
type UserResponse = User;
```

---

## Token-Efficient Prompt Templates

### Define Types

```
Define types for <DOMAIN>:
- Model types with base properties
- API request/response types
- Utility types for common operations
```

### Make Type-Safe

```
Refactor <CODE> to be type-safe:
- Add type annotations
- Replace `any` with specific types
- Add type guards
```

### Create Generic

```
Create generic <COMPONENT/FUNCTION>:
- Type parameter(s)
- Constraints
- Type inference
```

---

## Quick Reference

```typescript
// Type definitions
interface User {
  id: string;
}
type Status = "active" | "inactive";

// Utility types
type PartialUser = Partial<User>;
type UserUpdate = Omit<User, "id">;

// Generics
function identity<T>(value: T): T {
  return value;
}

// Type guards
function isUser(data: unknown): data is User {
  return typeof data === "object" && "id" in data;
}

// Discriminated unions
type Result<T> =
  | { type: "success"; data: T }
  | { type: "error"; message: string };

// Type inference
const users = fetchData<User[]>("/api/users");
```

---

## Important Reminders

1. **No `any`** - Use `unknown` or specific types
2. **Type inference** - Let TypeScript infer when possible
3. **Strict mode** - Always use strict settings
4. **Utility types** - Reuse built-in utilities
5. **Type guards** - Prefer over assertions
6. **Discriminated unions** - For variant types
7. **Consistent naming** - Follow naming conventions
8. **Document complex types** - When necessary

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/barisariburnu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
