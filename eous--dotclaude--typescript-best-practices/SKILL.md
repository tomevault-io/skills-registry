---
name: typescript-best-practices
description: TypeScript development best practices, patterns, and conventions. Use when writing TypeScript code, reviewing .ts/.tsx files, discussing type safety, generics, utility types, or TypeScript project configuration. Triggers on mentions of TypeScript, tsconfig, type inference, generics, discriminated unions, React TypeScript. Use when this capability is needed.
metadata:
  author: eous
---

# TypeScript Best Practices

## Type System Fundamentals

### Prefer Type Inference
```typescript
// Good - let TypeScript infer
const items = [1, 2, 3];
const user = { name: "Alice", age: 30 };

// Explicit when inference isn't sufficient
const handlers: Map<string, () => void> = new Map();
```

### Use Strict Mode
```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "exactOptionalPropertyTypes": true
  }
}
```

### Discriminated Unions Over Optional Properties
```typescript
// Bad
interface Response {
  data?: User;
  error?: string;
}

// Good
type Response =
  | { status: 'success'; data: User }
  | { status: 'error'; error: string };
```

## Type Patterns

### Utility Types
```typescript
// Partial - all properties optional
type PartialUser = Partial<User>;

// Required - all properties required
type RequiredConfig = Required<Config>;

// Pick/Omit - select properties
type UserPreview = Pick<User, 'id' | 'name'>;
type UserWithoutPassword = Omit<User, 'password'>;

// Record - typed dictionaries
type UserById = Record<string, User>;

// ReturnType/Parameters - function types
type Handler = ReturnType<typeof createHandler>;
```

### Generics
```typescript
// Constrained generics
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Default type parameters
interface Container<T = string> {
  value: T;
}

// Generic constraints with interfaces
interface Identifiable { id: string; }
function findById<T extends Identifiable>(items: T[], id: string): T | undefined {
  return items.find(item => item.id === id);
}
```

### Type Guards
```typescript
// Custom type guards
function isUser(value: unknown): value is User {
  return typeof value === 'object' && value !== null && 'id' in value;
}

// Assertion functions
function assertNonNull<T>(value: T): asserts value is NonNullable<T> {
  if (value === null || value === undefined) {
    throw new Error('Value is null or undefined');
  }
}
```

## React TypeScript Patterns

### Component Props
```typescript
// Props with children
interface CardProps {
  title: string;
  children: React.ReactNode;
}

// Event handlers
interface ButtonProps {
  onClick: (event: React.MouseEvent<HTMLButtonElement>) => void;
}

// Extending native elements
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
}
```

### Hooks
```typescript
// Typed useState
const [user, setUser] = useState<User | null>(null);

// Typed useRef
const inputRef = useRef<HTMLInputElement>(null);

// Typed useReducer
type Action = { type: 'increment' } | { type: 'set'; payload: number };
const [state, dispatch] = useReducer(reducer, initialState);
```

### Generic Components
```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return <ul>{items.map(renderItem)}</ul>;
}
```

## Error Handling

### Result Types
```typescript
type Result<T, E = Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };

async function fetchUser(id: string): Promise<Result<User>> {
  try {
    const user = await api.getUser(id);
    return { ok: true, value: user };
  } catch (error) {
    return { ok: false, error: error as Error };
  }
}
```

### Exhaustive Checks
```typescript
function assertNever(value: never): never {
  throw new Error(`Unexpected value: ${value}`);
}

function handleStatus(status: Status) {
  switch (status) {
    case 'pending': return 'Waiting...';
    case 'success': return 'Done!';
    case 'error': return 'Failed';
    default: return assertNever(status);
  }
}
```

## Module Patterns

### Barrel Exports
```typescript
// components/index.ts
export { Button } from './Button';
export { Input } from './Input';
export type { ButtonProps, InputProps } from './types';
```

### Type-Only Imports
```typescript
import type { User } from './types';
import { createUser } from './api';
```

## Anti-Patterns to Avoid

- `any` type (use `unknown` instead)
- Type assertions without validation (`as User`)
- Non-null assertions (`!`) without checks
- Overly complex conditional types
- Interface over type when type suffices
- Excessive use of enums (prefer const objects or unions)

## Tooling

- **ESLint**: `@typescript-eslint/eslint-plugin`
- **Prettier**: Format on save
- **ts-node**: Development execution
- **tsx**: Faster dev execution with esbuild

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eous) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
