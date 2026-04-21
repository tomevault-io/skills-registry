---
name: frontend
description: This skill provides comprehensive guidance for React and TypeScript development Use when this capability is needed.
metadata:
  author: donnadieu
---

# Frontend Development for Continuum

This skill provides comprehensive guidance for React and TypeScript development
in the Continuum platform.

## When to Use This Skill

Invoke this skill for any frontend development task:

- Creating new React components
- Refactoring existing components
- Styling with Tailwind CSS
- Implementing data fetching with the custom API wrapper
- Writing or updating TypeScript types
- Creating or updating tests with Vitest
- Working with React Router v7

## Core Principles

1. **Tailwind for styling** - Use Tailwind CSS with shadcn/ui and `cn()` utility
2. **React Query for data** - Use `@tanstack/react-query` with custom hooks
3. **Supabase auth** - Use Supabase client for authentication
4. **TypeScript strict mode** - No `any` types
5. **shadcn/ui components** - Use shadcn/ui primitives from `@/components/ui`
6. **Named exports** - Default exports only for page entry components
7. **User-focused testing** - Test behavior with Vitest + RTL

## Continuum Project Structure

```
frontend/src/
├── components/
│   ├── ui/                # shadcn/ui primitives
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── dialog.tsx
│   │   └── input.tsx
│   ├── chat/              # Chat feature components
│   │   ├── chat-interface.tsx
│   │   ├── chat-input.tsx
│   │   ├── message-list.tsx
│   │   └── citation-card.tsx
│   ├── onboarding/        # Onboarding flow
│   └── layout.tsx         # App shell
├── hooks/                 # Custom React hooks
│   ├── use-auth.ts
│   ├── use-chat.ts
│   └── use-entities.ts
├── lib/                   # Core utilities
│   ├── api.ts            # API wrapper with auth
│   ├── supabase.ts       # Supabase client
│   ├── queryClient.ts    # React Query config
│   └── utils.ts          # cn() utility
├── pages/                 # Route pages
│   ├── dashboard/
│   ├── chat/
│   └── settings/
├── providers/             # React context providers
│   └── auth-provider.tsx
├── types/                 # TypeScript types
│   └── index.ts
└── router.tsx            # React Router config
```

## Workflow: Choose Your Task

Before proceeding, identify the type of work needed and load the appropriate
reference documentation.

### For Styling Work

**When:** Adding styles, updating UI appearance, using Tailwind classes

**Read:** `references/styling.md`

Common patterns:

- Use `cn()` utility for conditional classes
- Tailwind utility classes for all styling
- Theme variants via props (e.g., `theme?: 'light' | 'dark'`)
- Radix UI for accessible primitives

### For Data Fetching Work

**When:** Calling APIs, fetching data, handling responses

**Read:** `references/data-fetching.md`

Common patterns:

- Use React Query hooks (`useQuery`, `useMutation`)
- Use `api` wrapper from `@/lib/api` for HTTP requests
- Type-safe responses with TypeScript generics
- Handle loading/error states in components
- Invalidate queries after mutations

### For Chat Interface Work

**When:** Building chat UI, handling citations, implementing messages

**Read:** `references/chat-patterns.md`

Common patterns:

- Message components with citation support
- Streaming response handling
- Conversation state management
- Loading and typing indicators

### For Authentication Work

**When:** Implementing login/logout, protecting routes, user state

**Read:** `references/auth-patterns.md`

Common patterns:

- Use `useAuth` hook for authentication state
- Supabase client for sign in/out operations
- Protected routes with ProtectedRoute component
- API client with automatic auth headers

### For Component Structure Work

**When:** Creating new components, organizing files, extracting logic

**Read:** `references/components.md`

Common patterns:

- Functional components with TypeScript
- `React.ComponentProps<'element'>` for extending native props
- ForwardRef for input components
- Compound component pattern for complex UIs
- `React.memo()` for performance optimization

### For TypeScript Work

**When:** Defining types, adding interfaces, working with generics

**Read:** `references/typescript.md`

Common patterns:

- Interface for props (with Props suffix)
- Type for everything else
- Discriminated unions for API responses
- Local types in same file, global in `types/`

### For Testing Work

**When:** Writing tests, mocking APIs, testing interactions

**Read:** `references/testing.md`

Common patterns:

- Vitest + React Testing Library
- Storybook for component documentation
- `userEvent` for simulating interactions
- Semantic queries: `getByRole()`, `getByText()`
- Extract test helper functions

## Quick Decision Tree

```
Is this about...
├─ Appearance/styling? → Read styling.md
├─ API calls/data fetching? → Read data-fetching.md
├─ Chat UI/citations? → Read chat-patterns.md
├─ Login/auth/protected routes? → Read auth-patterns.md
├─ File organization/hooks? → Read components.md
├─ Types/interfaces? → Read typescript.md
├─ Tests? → Read testing.md
└─ Modernizing old code? → Read refactoring.md
```

## Common Continuum Patterns

### cn() Utility for Class Names

```typescript
import { cn } from '@/lib/utils';

// Conditional classes
<button className={cn(
  'rounded-lg font-medium',
  variant === 'primary' && 'bg-primary text-primary-foreground',
  variant === 'secondary' && 'bg-secondary text-secondary-foreground',
  disabled && 'opacity-50 cursor-not-allowed'
)}>
```

### Supabase Client

```typescript
// lib/supabase.ts
import { createClient } from '@supabase/supabase-js';

export const supabase = createClient(
  import.meta.env.VITE_SUPABASE_URL,
  import.meta.env.VITE_SUPABASE_ANON_KEY
);

// Usage in components
import { supabase } from '@/lib/supabase';

// Auth operations
const { data, error } = await supabase.auth.signInWithPassword({
  email,
  password,
});

// Get current user
const { data: { user } } = await supabase.auth.getUser();

// API calls with auth
const { data, error } = await supabase
  .from('entities')
  .select('*')
  .eq('user_id', user.id);
```

### Auth Hook Pattern

```typescript
// hooks/use-auth.ts
import { useQuery, useMutation } from '@tanstack/react-query';
import { supabase } from '@/lib/supabase';

export function useAuth() {
  const { data: user, isLoading } = useQuery({
    queryKey: ['user'],
    queryFn: async () => {
      const { data: { user } } = await supabase.auth.getUser();
      return user;
    },
  });

  const login = useMutation({
    mutationFn: async ({ email, password }) => {
      const { data, error } = await supabase.auth.signInWithPassword({
        email,
        password,
      });
      if (error) throw error;
      return data;
    },
  });

  return { user, isLoading, login };
}
```

### Component with ForwardRef

```typescript
import { forwardRef } from 'react';
import { cn } from '@/lib/utils';

interface InputProps extends React.ComponentProps<'input'> {
  error?: string;
}

export const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ className, error, ...props }, ref) => {
    return (
      <div className="space-y-1">
        <input
          ref={ref}
          className={cn(
            'flex h-10 w-full rounded-md border border-input bg-background px-3 py-2',
            'focus:outline-none focus:ring-2 focus:ring-ring',
            error && 'border-red-500',
            className
          )}
          {...props}
        />
        {error && <p className="text-sm text-red-500">{error}</p>}
      </div>
    );
  }
);

Input.displayName = 'Input';
```

### Custom Hook Pattern

```typescript
import { useState, useCallback } from 'react';

interface UseFileUploadOptions {
  maxSize?: number;
  allowedTypes?: string[];
}

export function useFileUpload(options: UseFileUploadOptions = {}) {
  const [file, setFile] = useState<File | null>(null);
  const [error, setError] = useState<string | null>(null);
  const [uploading, setUploading] = useState(false);

  const upload = useCallback(async (file: File) => {
    setUploading(true);
    setError(null);
    try {
      // Upload logic
    } catch (e) {
      setError(e instanceof Error ? e.message : 'Upload failed');
    } finally {
      setUploading(false);
    }
  }, []);

  return { file, error, uploading, upload, setFile };
}
```

### React Router v7 with Loaders

```typescript
// router.tsx
{
  path: '/dashboard',
  lazy: async () => {
    const { DashboardPage } = await import('@/pages/dashboard');
    return {
      Component: DashboardPage,
      loader: async () => {
        const user = await authStore.getUser();
        if (!user) return redirect('/login');
        return { user };
      },
    };
  },
}
```

## Multi-File Changes

When making changes that span multiple files:

1. **Read all affected files completely** before making changes
2. **Identify all references** using global search
3. **Update in dependency order**:
   - Types first (`types/`)
   - Utilities next (`utils/`, `lib/`)
   - Hooks (`hooks/`)
   - Components last (`components/`)
4. **Test incrementally** after each major change

## Before You Start

1. Identify the task type using the decision tree above
2. Read the appropriate reference file(s) for your task
3. Review existing similar code for consistency
4. Check `types/` for existing types before creating new ones
5. Verify no duplicate functionality exists

## After Implementation

1. Ensure all imports use `@/` alias for project files
2. Verify TypeScript has no errors (no `any` types)
3. Check loading and error states for data fetching
4. Confirm accessibility with semantic HTML
5. Run tests: `pnpm test`
6. Run Storybook if component has stories

## Getting Help

If uncertain about:

- **Component structure** → Look at `components/base/`
- **API patterns** → Check `lib/api.ts`
- **Styling** → Review existing components for Tailwind patterns
- **Types** → Browse `types/`

## Reference Files

All reference files are in `references/` directory:

- `core-patterns.md` - Project overview and structure
- `styling.md` - Tailwind CSS, cn(), shadcn/ui
- `data-fetching.md` - React Query, API wrapper
- `chat-patterns.md` - Chat UI, citations, streaming
- `auth-patterns.md` - Supabase auth, protected routes
- `components.md` - Component structure, hooks
- `typescript.md` - Types and interfaces
- `testing.md` - Vitest, RTL, Storybook patterns
- `refactoring.md` - Modernizing existing code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/donnadieu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
