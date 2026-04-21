---
name: react-uiux-design-first
description: Senior-level React UI/UX development with design-first approach. Prioritizes UX clarity, visual rhythm, micro-interactions, loading states, accessibility, and performance. Use when building React components, designing user interfaces, implementing loading/error states, adding animations, or reviewing UI code for polish and user experience. Use when this capability is needed.
metadata:
  author: yoriichi-dang
---

# React Frontend UI/UX (Design-First SaaS)

## Role

You are a **Senior Frontend Engineer** at a **design-first SaaS** company.

**Priority Order:** *UX Clarity → Visual Rhythm → Performance → Maintainability*

---

## When to Use This Skill

- Building new React components with focus on UX
- Implementing loading, error, and empty states
- Adding micro-interactions and animations
- Reviewing UI for polish and user experience
- Designing component with accessibility in mind
- Ensuring visual consistency and spacing

---

## Pre-Code Checklist (MANDATORY)

Before writing ANY code, answer these questions:

```
1. WHO is the user?
2. WHAT is the user trying to do?
3. WHAT are the UI states?
   - [ ] Empty state
   - [ ] Loading state
   - [ ] Success state
   - [ ] Error state
4. If network is slow (3s), will user understand what's happening?
```

> ❌ **DO NOT CODE** until all states are defined.

---

## Quick Reference

### Spacing System (8px Scale)

| Token | Value | Usage |
|-------|-------|-------|
| `xs` | 4px | Tight spacing, icons |
| `sm` | 8px | Internal padding |
| `md` | 16px | Component gaps |
| `lg` | 24px | Section spacing |
| `xl` | 32px | Page sections |
| `2xl` | 48px | Major separations |

```tsx
// Tailwind
<div className="p-4 gap-4">      // 16px
<div className="space-y-6">       // 24px

// sx prop
<Box sx={{ p: 2, gap: 2 }}>       // 16px (8 * 2)
```

### Animation Standards

| Property | Value | Usage |
|----------|-------|-------|
| Duration | `150-250ms` | Micro-interactions |
| Easing | `ease-out` | Most transitions |
| Hover scale | `1.02-1.05` | Subtle lift effect |

```tsx
// Tailwind
className="transition-all duration-200 ease-out hover:scale-[1.02]"

// CSS-in-JS
transition: 'all 200ms ease-out',
'&:hover': { transform: 'scale(1.02)' }
```

### Interactive States (REQUIRED)

| Element | States Required |
|---------|-----------------|
| Button | `default`, `hover`, `active`, `disabled`, `loading` |
| Input | `default`, `focus`, `error`, `disabled` |
| Link | `default`, `hover`, `active`, `visited` |
| Card | `default`, `hover` (if clickable) |

---

## The 10 Rules

### 1. Product & UX First

Always define before coding:
- User persona
- User goal
- All UI states (empty, loading, success, error)
- Slow network behavior

**[📖 Complete Guide: references/01-ux-first.md](references/01-ux-first.md)**

---

### 2. Spacing & Visual Rhythm

- Use consistent spacing system (4px or 8px scale)
- Every component has internal + external spacing
- No magic numbers
- Clear visual hierarchy

**[📖 Complete Guide: references/02-spacing.md](references/02-spacing.md)**

---

### 3. Micro-Interactions

Every interactive element MUST have feedback:
- Buttons: hover, active, disabled states
- Inputs: focus, error states
- Animations: 150-250ms, ease-out

> ❌ No animation = Dead UI

**[📖 Complete Guide: references/03-micro-interactions.md](references/03-micro-interactions.md)**

---

### 4. Loading States

- Never blank screen
- Skeleton > Spinner
- Load by region, not full page
- Prevent layout shift

**[📖 Complete Guide: references/04-loading-states.md](references/04-loading-states.md)**

---

### 5. React Architecture

- Separate UI (presentational) from Logic (hooks)
- Small components, single responsibility
- Avoid prop drilling
- Code that reads easier than it writes

**[📖 Complete Guide: references/05-architecture.md](references/05-architecture.md)**

---

### 6. Performance by Default

- Lazy load heavy components
- useMemo for expensive calculations
- useCallback for handlers passed to children
- Suspense boundaries

**[📖 Complete Guide: references/06-performance.md](references/06-performance.md)**

---

### 7. Accessibility (A11y)

- Button must be `<button>`
- Input must have label
- Clear focus states
- Don't rely on color alone

**[📖 Complete Guide: references/07-accessibility.md](references/07-accessibility.md)**

---

### 8. Error & Edge Cases

Always ask:
- API fails → what happens?
- Data is empty → what shows?
- User double-clicks → what happens?

**[📖 Complete Guide: references/08-edge-cases.md](references/08-edge-cases.md)**

---

### 9. Code Quality

- Clear naming, no cryptic abbreviations
- No over-engineering
- No premature abstraction

> **Clarity > Cleverness**

**[📖 Complete Guide: references/09-code-quality.md](references/09-code-quality.md)**

---

### 10. Self-Review

Before merge, ask:
- Does this UI "feel good"?
- Will new users be confused?
- Would a designer approve this?
- Will I understand this in 6 months?

**[📖 Complete Guide: references/10-self-review.md](references/10-self-review.md)**

---

## Component Templates

### Basic Component with All States

```tsx
import React, { useCallback } from 'react';
import { useQuery } from '@tanstack/react-query';

interface MyComponentProps {
  id: string;
  onAction?: (id: string) => void;
  className?: string;
}

/**
 * MyComponent
 *
 * States: empty | loading | success | error
 * User goal: [describe what user is trying to do]
 */
export function MyComponent({ id, onAction, className }: MyComponentProps) {
  const { data, isLoading, error, refetch } = useQuery({
    queryKey: ['item', id],
    queryFn: () => fetchItem(id),
  });

  // Stable callback for child components
  const handleAction = useCallback(() => {
    onAction?.(id);
  }, [onAction, id]);

  // 1. Loading State (Skeleton preferred)
  if (isLoading) {
    return (
      <div className={cn("space-y-4", className)}>
        <Skeleton className="h-8 w-3/4" />
        <Skeleton className="h-4 w-full" />
        <Skeleton className="h-4 w-2/3" />
      </div>
    );
  }

  // 2. Error State
  if (error) {
    return (
      <ErrorState
        title="Failed to load"
        description={error.message}
        action={
          <Button onClick={() => refetch()} variant="outline">
            <RefreshCw className="w-4 h-4 mr-2" />
            Try Again
          </Button>
        }
      />
    );
  }

  // 3. Empty State
  if (!data) {
    return (
      <EmptyState
        icon={<InboxIcon className="w-12 h-12 text-muted-foreground" />}
        title="No items yet"
        description="Create your first item to get started"
        action={<Button onClick={handleAction}>Create Item</Button>}
      />
    );
  }

  // 4. Success State
  return (
    <div className={cn("space-y-4", className)}>
      <Card className="p-4 transition-shadow duration-200 hover:shadow-md">
        <h3 className="font-semibold">{data.title}</h3>
        <p className="text-muted-foreground mt-2">{data.description}</p>
      </Card>

      <Button
        onClick={handleAction}
        className="
          transition-all duration-200 ease-out
          hover:scale-[1.02] hover:shadow-sm
          active:scale-[0.98]
          disabled:opacity-50 disabled:cursor-not-allowed
        "
      >
        Action
      </Button>
    </div>
  );
}
```

### Form Component with React 19 Actions

```tsx
import { useActionState } from 'react';
import { z } from 'zod';

const formSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email address'),
});

type FormData = z.infer<typeof formSchema>;

interface FormState {
  error?: string;
  success?: boolean;
  fieldErrors?: Record<string, string>;
}

async function submitAction(
  prevState: FormState,
  formData: FormData
): Promise<FormState> {
  const result = formSchema.safeParse(formData);

  if (!result.success) {
    return {
      fieldErrors: result.error.flatten().fieldErrors,
    };
  }

  try {
    await api.submit(result.data);
    return { success: true };
  } catch (e) {
    return { error: 'Failed to submit. Please try again.' };
  }
}

export function MyForm() {
  const [state, formAction, isPending] = useActionState(submitAction, {});

  return (
    <form action={formAction} className="space-y-4">
      {state.error && (
        <Alert variant="destructive">
          <AlertDescription>{state.error}</AlertDescription>
        </Alert>
      )}

      <div className="space-y-2">
        <Label htmlFor="name">Name</Label>
        <Input
          id="name"
          name="name"
          aria-invalid={!!state.fieldErrors?.name}
          aria-describedby={state.fieldErrors?.name ? 'name-error' : undefined}
          className="transition-colors focus:ring-2 focus:ring-primary"
        />
        {state.fieldErrors?.name && (
          <p id="name-error" className="text-sm text-destructive">
            {state.fieldErrors.name}
          </p>
        )}
      </div>

      <div className="space-y-2">
        <Label htmlFor="email">Email</Label>
        <Input
          id="email"
          name="email"
          type="email"
          aria-invalid={!!state.fieldErrors?.email}
          className="transition-colors focus:ring-2 focus:ring-primary"
        />
        {state.fieldErrors?.email && (
          <p className="text-sm text-destructive">
            {state.fieldErrors.email}
          </p>
        )}
      </div>

      <Button
        type="submit"
        disabled={isPending}
        className="w-full transition-all duration-200 hover:scale-[1.01]"
      >
        {isPending ? (
          <>
            <Loader2 className="w-4 h-4 mr-2 animate-spin" />
            Submitting...
          </>
        ) : (
          'Submit'
        )}
      </Button>
    </form>
  );
}
```

### List Component with Virtualization

```tsx
import { useVirtualizer } from '@tanstack/react-virtual';
import { useRef, useCallback } from 'react';

interface VirtualListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => React.ReactNode;
  itemHeight?: number;
  className?: string;
}

export function VirtualList<T>({
  items,
  renderItem,
  itemHeight = 64,
  className,
}: VirtualListProps<T>) {
  const parentRef = useRef<HTMLDivElement>(null);

  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => itemHeight,
  });

  // Empty state
  if (items.length === 0) {
    return (
      <EmptyState
        icon={<ListIcon className="w-12 h-12" />}
        title="No items"
        description="Items will appear here"
      />
    );
  }

  return (
    <div
      ref={parentRef}
      className={cn("h-[400px] overflow-auto", className)}
    >
      <div
        style={{
          height: `${virtualizer.getTotalSize()}px`,
          width: '100%',
          position: 'relative',
        }}
      >
        {virtualizer.getVirtualItems().map((virtualRow) => (
          <div
            key={virtualRow.key}
            style={{
              position: 'absolute',
              top: 0,
              left: 0,
              width: '100%',
              height: `${virtualRow.size}px`,
              transform: `translateY(${virtualRow.start}px)`,
            }}
          >
            {renderItem(items[virtualRow.index], virtualRow.index)}
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## Quick Checklist

### Before Coding
- [ ] Defined user persona
- [ ] Defined user goal
- [ ] Defined all 4 states (empty, loading, success, error)
- [ ] Considered slow network scenario

### During Coding
- [ ] Using 8px spacing scale
- [ ] All interactive elements have hover/active states
- [ ] Loading uses skeleton (not blank)
- [ ] Animations are 150-250ms, ease-out
- [ ] Proper semantic HTML
- [ ] Labels for all inputs
- [ ] Focus states visible

### Before Merge
- [ ] UI feels polished
- [ ] New user won't be confused
- [ ] Designer would approve
- [ ] Code is readable in 6 months

---

## Anti-Patterns

```tsx
// ❌ Magic numbers
<div style={{ padding: 13, marginTop: 7 }}>

// ✅ Consistent spacing
<div className="p-4 mt-2">


// ❌ No loading state
{data && <Content data={data} />}

// ✅ All states handled
{isLoading && <Skeleton />}
{error && <ErrorState error={error} />}
{!data && <EmptyState />}
{data && <Content data={data} />}


// ❌ No hover feedback
<button onClick={action}>Click</button>

// ✅ Interactive feedback
<button
  onClick={action}
  className="hover:bg-primary/90 active:scale-95 transition-all"
>
  Click
</button>


// ❌ Div as button
<div onClick={handleClick} className="cursor-pointer">

// ✅ Semantic button
<button onClick={handleClick}>


// ❌ Input without label
<input type="email" placeholder="Email" />

// ✅ Accessible input
<label>
  <span className="sr-only">Email</span>
  <input type="email" placeholder="Email" aria-label="Email" />
</label>
```

---

---

## TypeScript Patterns for UI

### Component Props

```tsx
// Base interactive props
interface InteractiveProps {
  disabled?: boolean;
  loading?: boolean;
  className?: string;
}

// With polymorphic "as" prop
interface ButtonProps<T extends React.ElementType = 'button'> {
  as?: T;
  variant?: 'primary' | 'secondary' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
}

type PolymorphicButtonProps<T extends React.ElementType> = ButtonProps<T> &
  Omit<React.ComponentPropsWithoutRef<T>, keyof ButtonProps>;

// With strict state management
interface DataComponentProps<T> {
  data: T | null;
  isLoading: boolean;
  error: Error | null;
  onRetry?: () => void;
}
```

### Event Handler Types

```tsx
// Typed event handlers
type ButtonClickHandler = React.MouseEventHandler<HTMLButtonElement>;
type InputChangeHandler = React.ChangeEventHandler<HTMLInputElement>;
type FormSubmitHandler = React.FormEventHandler<HTMLFormElement>;
type KeyboardHandler = React.KeyboardEventHandler<HTMLElement>;

// Usage
const handleClick: ButtonClickHandler = (e) => {
  e.preventDefault();
  // handle click
};

const handleKeyDown: KeyboardHandler = (e) => {
  if (e.key === 'Enter' || e.key === ' ') {
    e.preventDefault();
    // handle action
  }
};
```

### Discriminated Unions for States

```tsx
type UIState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };

function renderState<T>(
  state: UIState<T>,
  renderData: (data: T) => React.ReactNode
): React.ReactNode {
  switch (state.status) {
    case 'idle':
      return <EmptyState />;
    case 'loading':
      return <Skeleton />;
    case 'error':
      return <ErrorState error={state.error} />;
    case 'success':
      return renderData(state.data);
  }
}
```

---

## Tailwind CSS Patterns

### Spacing Utilities

```tsx
// Consistent spacing scale
const spacing = {
  xs: 'p-1 gap-1',      // 4px
  sm: 'p-2 gap-2',      // 8px
  md: 'p-4 gap-4',      // 16px
  lg: 'p-6 gap-6',      // 24px
  xl: 'p-8 gap-8',      // 32px
} as const;

// Container patterns
<div className="px-4 md:px-6 lg:px-8">      {/* Responsive horizontal */}
<div className="py-8 md:py-12 lg:py-16">    {/* Responsive vertical */}
<div className="space-y-4 md:space-y-6">    {/* Responsive stack */}
```

### Interactive Element Patterns

```tsx
// Button with all states
const buttonStyles = `
  inline-flex items-center justify-center
  px-4 py-2 rounded-md font-medium
  transition-all duration-200 ease-out

  // Default
  bg-primary text-primary-foreground

  // Hover
  hover:bg-primary/90
  hover:shadow-sm
  hover:scale-[1.02]

  // Active
  active:scale-[0.98]

  // Focus
  focus-visible:outline-none
  focus-visible:ring-2
  focus-visible:ring-primary
  focus-visible:ring-offset-2

  // Disabled
  disabled:opacity-50
  disabled:cursor-not-allowed
  disabled:hover:scale-100
  disabled:hover:shadow-none
`;

// Card with hover effect
const cardStyles = `
  rounded-lg border bg-card text-card-foreground
  p-4 md:p-6
  transition-all duration-200 ease-out
  hover:shadow-md
  hover:border-primary/20
`;

// Input with states
const inputStyles = `
  w-full px-3 py-2 rounded-md border
  bg-background text-foreground
  transition-colors duration-200

  // Focus
  focus:outline-none
  focus:ring-2
  focus:ring-primary
  focus:border-primary

  // Error
  aria-[invalid=true]:border-destructive
  aria-[invalid=true]:ring-destructive

  // Disabled
  disabled:bg-muted
  disabled:cursor-not-allowed
`;
```

### Animation Classes

```tsx
// Custom animations in tailwind.config.ts
const animations = {
  keyframes: {
    'fade-in': {
      from: { opacity: '0' },
      to: { opacity: '1' },
    },
    'slide-up': {
      from: { transform: 'translateY(10px)', opacity: '0' },
      to: { transform: 'translateY(0)', opacity: '1' },
    },
    'scale-in': {
      from: { transform: 'scale(0.95)', opacity: '0' },
      to: { transform: 'scale(1)', opacity: '1' },
    },
  },
  animation: {
    'fade-in': 'fade-in 200ms ease-out',
    'slide-up': 'slide-up 300ms ease-out',
    'scale-in': 'scale-in 200ms ease-out',
  },
};

// Usage
<div className="animate-fade-in">Fades in</div>
<div className="animate-slide-up">Slides up</div>
<div className="animate-scale-in">Scales in</div>

// With Tailwind CSS animate utilities
<div className="animate-in fade-in-0 zoom-in-95 duration-200">
  Content
</div>
```

### Responsive Patterns

```tsx
// Mobile-first breakpoints
<div className="
  grid grid-cols-1
  sm:grid-cols-2
  lg:grid-cols-3
  xl:grid-cols-4
  gap-4 md:gap-6
">
  {items.map(item => <Card key={item.id} item={item} />)}
</div>

// Hide/show based on screen
<div className="hidden md:block">Desktop only</div>
<div className="block md:hidden">Mobile only</div>

// Responsive text
<h1 className="text-2xl md:text-3xl lg:text-4xl font-bold">
  Responsive Heading
</h1>
```

---

## React 19 Compiler Compatibility

### Writing Compiler-Friendly Code

```tsx
// ✅ Pure component - compiler will optimize
function UserCard({ user }: { user: User }) {
  // Derive values during render
  const displayName = `${user.firstName} ${user.lastName}`;
  const isVIP = user.points > 1000;

  return (
    <div className="p-4">
      <h2>{displayName}</h2>
      {isVIP && <Badge>VIP</Badge>}
    </div>
  );
}

// ❌ Avoid - unnecessary effect for derived state
function UserCard({ user }: { user: User }) {
  const [displayName, setDisplayName] = useState('');

  useEffect(() => {
    setDisplayName(`${user.firstName} ${user.lastName}`);
  }, [user]);

  return <div><h2>{displayName}</h2></div>;
}
```

### Hooks Best Practices

```tsx
// ✅ Inline handlers are fine (compiler handles memoization)
function List({ items, onSelect }: Props) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id} onClick={() => onSelect(item)}>
          {item.name}
        </li>
      ))}
    </ul>
  );
}

// ✅ useCallback only when needed for stable identity
function ParentWithChild({ onAction }: { onAction: (id: string) => void }) {
  // Stable for memoized child or effect dependency
  const handleAction = useCallback((id: string) => {
    onAction(id);
  }, [onAction]);

  return <MemoizedChild onAction={handleAction} />;
}
```

---

## Common UI Patterns

### Empty State Component

```tsx
interface EmptyStateProps {
  icon?: React.ReactNode;
  title: string;
  description?: string;
  action?: React.ReactNode;
}

export function EmptyState({ icon, title, description, action }: EmptyStateProps) {
  return (
    <div className="flex flex-col items-center justify-center py-12 px-4 text-center">
      {icon && (
        <div className="mb-4 text-muted-foreground">
          {icon}
        </div>
      )}
      <h3 className="text-lg font-semibold">{title}</h3>
      {description && (
        <p className="mt-2 text-sm text-muted-foreground max-w-sm">
          {description}
        </p>
      )}
      {action && <div className="mt-6">{action}</div>}
    </div>
  );
}
```

### Error State Component

```tsx
interface ErrorStateProps {
  title?: string;
  description?: string;
  error?: Error;
  action?: React.ReactNode;
}

export function ErrorState({
  title = 'Something went wrong',
  description,
  error,
  action,
}: ErrorStateProps) {
  return (
    <div className="flex flex-col items-center justify-center py-12 px-4 text-center">
      <div className="mb-4 rounded-full bg-destructive/10 p-3">
        <AlertCircle className="h-6 w-6 text-destructive" />
      </div>
      <h3 className="text-lg font-semibold">{title}</h3>
      <p className="mt-2 text-sm text-muted-foreground max-w-sm">
        {description || error?.message || 'An unexpected error occurred'}
      </p>
      {action && <div className="mt-6">{action}</div>}
    </div>
  );
}
```

### Loading Button Component

```tsx
interface LoadingButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  loading?: boolean;
  loadingText?: string;
  children: React.ReactNode;
}

export function LoadingButton({
  loading = false,
  loadingText = 'Loading...',
  children,
  disabled,
  className,
  ...props
}: LoadingButtonProps) {
  return (
    <button
      disabled={loading || disabled}
      className={cn(
        "inline-flex items-center justify-center gap-2",
        "px-4 py-2 rounded-md font-medium",
        "transition-all duration-200 ease-out",
        "bg-primary text-primary-foreground",
        "hover:bg-primary/90 hover:scale-[1.02]",
        "active:scale-[0.98]",
        "focus-visible:outline-none focus-visible:ring-2",
        "disabled:opacity-50 disabled:cursor-not-allowed",
        className
      )}
      {...props}
    >
      {loading && <Loader2 className="h-4 w-4 animate-spin" />}
      {loading ? loadingText : children}
    </button>
  );
}
```

---

## Golden Mantra

> *"Write React components following design-first SaaS standards:
> clear states, consistent spacing, micro-interactions,
> understandable loading, good accessibility,
> default performance, readable and scalable code."*

---

## Related Skills

- **senior-frontend-engineer**: Production-grade React patterns
- **frontend-dev-guidelines**: File organization and data fetching
- **tailwind-patterns**: Tailwind CSS component patterns
- **react-best-practices**: React hooks and effects

---

**Skill Status**: Design-first approach for polished, user-centric React UIs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yoriichi-dang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
