---
name: screaming-architecture
description: Organize code by feature intent, not framework layers. Structure projects to clearly communicate business purpose at first glance. Use when this capability is needed.
metadata:
  author: acbcdev
---

# Screaming Architecture Skill

Screaming Architecture is a design philosophy where your folder structure **screams the purpose** of the application. Organize by feature and business domain, not by technical type.

---

## Core Principle

**Group by feature, not by type.**

❌ Bad: `components/`, `hooks/`, `utils/`, `contexts/` (framework-focused)

✅ Good: `features/todos/`, `features/auth/`, `features/dashboard/` (feature-focused)

---

## Project Structure

### Top-Level Organization

```
src/
├── features/           # Business features and domains
│   ├── todos/
│   ├── auth/
│   ├── dashboard/
│   └── settings/
├── common/             # Truly shared utilities (used by 3+ features)
│   ├── ui/            # Reusable UI components
│   ├── hooks/         # Reusable custom hooks
│   ├── utils/         # Utility functions
│   ├── types/         # Global type definitions
│   └── providers/     # Global providers (Theme, Auth, etc.)
├── lib/               # Framework/library integrations
├── config/            # App-wide configuration
└── app.tsx            # Root component
```

**Rule**: Features own their code; common utilities are truly cross-cutting.

---

## Feature Folder Structure

Each feature is self-contained with all its code together.

```
features/todos/
├── add-todo-form/
│   ├── add-todo-form.tsx       # Component
│   ├── add-todo-form.test.tsx   # Tests
│   └── add-todo-form.module.css # Styles (optional)
├── todo-list/
│   ├── todo-list.tsx
│   ├── todo-list.test.tsx
│   └── todo-item.tsx            # Sub-component
├── todo-provider/
│   ├── todo-provider.tsx        # Context provider
│   └── todo-context.ts          # Context definition
├── use-todo/
│   └── use-todo.ts              # Custom hook
├── types.ts                     # Feature-scoped types
├── index.ts                     # Barrel file (exports)
└── README.md                    # Feature documentation
```

**Rules**:
- One file per component/hook/utility
- Co-locate related code
- Use barrel files (index.ts) for clean imports
- No `index.tsx` - use descriptive filenames

---

## Naming Conventions

- **Folders**: `kebab-case` with clear feature names: `add-todo-form`, `user-profile`, `payment-modal`
- **Files**: `kebab-case.tsx` (not `index.tsx`): `todo-item.tsx`, `use-todos.ts`, `types.ts`
- **Components**: `PascalCase` in filename: `AddTodoForm.tsx`, `TodoList.tsx`
- **Hooks**: `use-` prefix: `use-todo.ts`, `use-todos.ts`
- **Utilities**: `verb-noun` format: `format-date.ts`, `validate-email.ts`
- **Types**: Plural if exported as barrel: `types.ts`
- **Styles**: Match component name: `add-todo-form.module.css`

```typescript
// ✅ Good
features/todos/add-todo-form/add-todo-form.tsx
features/todos/use-todo/use-todo.ts
features/todos/types.ts

// ❌ Bad
features/todos/AddTodoForm/index.tsx
features/todos/hooks/useTodo.ts
features/todos/utils/types.ts
```

---

## Barrel Files (index.ts)

Export public API from each feature using barrel files.

```typescript
// features/todos/index.ts
export { AddTodoForm } from './add-todo-form/add-todo-form';
export { TodoList } from './todo-list/todo-list';
export { TodoProvider } from './todo-provider/todo-provider';
export { useTodo } from './use-todo/use-todo';
export type { Todo, TodoContextType } from './types';

// Usage in other features
import { TodoList, useTodo } from 'features/todos';
```

**Rules**:
- Export components, hooks, and types
- DO NOT export internal utilities or sub-components
- Keep barrel files organized (components first, hooks, then types)
- One barrel file per feature (at feature root)

---

## Type Organization

### Feature-Scoped Types

Keep types with their feature in `types.ts`:

```typescript
// features/todos/types.ts
export interface Todo {
  id: string;
  title: string;
  completed: boolean;
  createdAt: Date;
}

export interface TodoContextType {
  todos: Todo[];
  addTodo: (title: string) => void;
  removeTodo: (id: string) => void;
}

// features/todos/todo-provider/todo-provider.tsx
import type { TodoContextType } from '../types';
```

### Shared Global Types

Put truly shared types in `shared/types/`:

```typescript
// shared/types/api.ts
export interface ApiResponse<T> {
  data: T;
  status: number;
  error?: string;
}

// shared/types/user.ts
export interface User {
  id: string;
  name: string;
  email: string;
}
```

**Rule**: Minimize global types; prefer feature-scoped types.

---

## Feature Dependencies

### DO

- ✅ Feature can depend on `common/`
- ✅ Feature can import from another feature's barrel file
- ✅ Features can use global providers (`common/providers/`)

### DO NOT

- ❌ Features should not directly import internals from other features
- ❌ Circular dependencies between features
- ❌ Common utilities importing from features

```typescript
// ✅ Good: Import from barrel file
import { TodoList, useTodo } from 'features/todos';

// ❌ Bad: Import internal implementation
import { TodoProvider } from 'features/todos/todo-provider/todo-provider';

// ❌ Bad: Feature depending on another feature's types directly
import { Todo } from 'features/todos/types';
// Use barrel: import type { Todo } from 'features/todos';
```

---

## Common Utilities

**When to extract to `common/`:**

1. **Used by 3+ features** - Sign it's truly shared
2. **Generic utilities** - Date formatting, string validation, API helpers
3. **Design tokens & UI components** - Buttons, inputs, modals (no business logic)
4. **Custom hooks** - Reusable patterns like `useLocalStorage`, `useApi`

**DO NOT put in common:**

```typescript
// ❌ Bad: Business logic in common
common/hooks/use-todo.ts  // This is todos feature logic

// ❌ Bad: Feature-specific utilities
common/utils/format-todo-date.ts  // This is todos feature logic

// ✅ Good: Truly generic
common/utils/format-date.ts
common/hooks/use-local-storage.ts
common/ui/button.tsx
```

---

## Common Patterns

### Feature with Context Provider

```
features/auth/
├── auth-provider/
│   ├── auth-provider.tsx
│   └── auth-context.ts
├── login-form/
│   └── login-form.tsx
├── use-auth/
│   └── use-auth.ts
├── types.ts
└── index.ts
```

### Feature with Multiple Views

```
features/dashboard/
├── dashboard-header/
│   └── dashboard-header.tsx
├── dashboard-content/
│   ├── dashboard-content.tsx
│   ├── chart-widget/
│   │   └── chart-widget.tsx
│   └── stats-widget/
│       └── stats-widget.tsx
├── types.ts
└── index.ts
```

### Feature with API Integration

```
features/todos/
├── add-todo-form/
│   └── add-todo-form.tsx
├── todo-list/
│   └── todo-list.tsx
├── api/
│   ├── get-todos.ts
│   ├── create-todo.ts
│   └── delete-todo.ts
├── types.ts
└── index.ts
```

---

## Benefits

| Benefit | Why |
|---------|-----|
| **Clarity** | Folder structure immediately shows business features, not technical layers |
| **Searchability** | Finding todo-related code is intuitive - look in `features/todos/` |
| **Scalability** | Easy to add new features without restructuring |
| **Collaboration** | Team members work on features, not layers; fewer merge conflicts |
| **Testability** | Features are isolated; easier to test feature logic independently |
| **Onboarding** | New developers see business domains immediately |

---

## Anti-Patterns to Avoid

### Excessive Nesting

```typescript
// ❌ Too deep
features/todos/components/forms/add-todo-form/add-todo-form.tsx

// ✅ Flat
features/todos/add-todo-form/add-todo-form.tsx
```

### Generic Folder Names

```typescript
// ❌ Unclear purpose
features/app/  // What is "app"?
features/common/  // What's common here?

// ✅ Clear feature names
features/todos/
features/auth/
features/settings/
```

### Feature-Specific Code in Shared

```typescript
// ❌ This is todos logic, not shared
shared/utils/format-todo-date.ts
shared/hooks/use-todo-validation.ts

// ✅ Keep in feature
features/todos/format-todo-date.ts
features/todos/use-todo-validation.ts
```

### Circular Dependencies

```typescript
// ❌ todos imports auth, auth imports todos
features/todos/ → features/auth/
features/auth/ → features/todos/

// ✅ Both depend on shared if needed
features/todos/ → shared/
features/auth/ → shared/
```

### Deep Component Nesting

```typescript
// ❌ Sub-components buried
features/todos/todo-list/components/list/item/todo-item.tsx

// ✅ Sub-components at feature level
features/todos/todo-item.tsx
features/todos/todo-list.tsx
```

---

## Quick Reference

- **Organize by feature**, not technical type
- **Co-locate** everything related to a feature
- **Use kebab-case** for files and folders
- **Avoid `index.tsx`** - use descriptive names
- **One barrel file** per feature exports public API
- **Keep types** with their feature
- **Share only what's truly generic** (3+ features or framework utilities)
- **No feature should import internals** from another feature
- **Flat hierarchy** - avoid excessive nesting

**The codebase should scream its business purpose.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acbcdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
