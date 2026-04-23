---
name: react-patterns
description: Use when building or refactoring React components. Covers hooks patterns, component composition, state management with Zustand/Context, error boundaries, and performance optimization.
metadata:
  author: canivel
---

# React Patterns and Best Practices

## Hooks Patterns

### useState - Prefer Derived State

```tsx
// BAD: redundant state that can be computed
const [items, setItems] = useState<Item[]>([]);
const [count, setCount] = useState(0); // derived from items.length

// GOOD: derive values from existing state
const [items, setItems] = useState<Item[]>([]);
const count = items.length;
```

### useEffect - Minimize and Isolate

```tsx
// BAD: multiple concerns in one effect
useEffect(() => {
  fetchUser(id);
  trackPageView(id);
  document.title = `User ${id}`;
}, [id]);

// GOOD: separate effects for separate concerns
useEffect(() => { fetchUser(id); }, [id]);
useEffect(() => { trackPageView(id); }, [id]);
useEffect(() => { document.title = `User ${id}`; }, [id]);

// GOOD: cleanup subscriptions
useEffect(() => {
  const controller = new AbortController();
  fetch(`/api/users/${id}`, { signal: controller.signal })
    .then(res => res.json())
    .then(setUser);
  return () => controller.abort();
}, [id]);
```

### useCallback and useMemo

```tsx
// Use useCallback when passing functions to memoized children
const handleDelete = useCallback((id: string) => {
  setItems(prev => prev.filter(item => item.id !== id));
}, []);

// Use useMemo for expensive computations
const sortedItems = useMemo(
  () => items.slice().sort((a, b) => a.name.localeCompare(b.name)),
  [items]
);

// NEVER wrap trivial calculations in useMemo
// BAD: const fullName = useMemo(() => `${first} ${last}`, [first, last]);
// GOOD: const fullName = `${first} ${last}`;
```

### useRef - DOM Access and Stable Values

```tsx
const inputRef = useRef<HTMLInputElement>(null);
const prevValueRef = useRef<string>('');

useEffect(() => {
  prevValueRef.current = value; // store previous value without re-render
}, [value]);

const focusInput = () => inputRef.current?.focus();
```

### Custom Hooks - Extract Reusable Logic

```tsx
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value);
  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  return debouncedValue;
}

function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });
  useEffect(() => { localStorage.setItem(key, JSON.stringify(value)); }, [key, value]);
  return [value, setValue] as const;
}
```

## Component Composition

```tsx
// Use children and render props over deeply nested props
// BAD:
<Card title="Settings" subtitle="Manage" icon={<GearIcon />} footer={<Button>Save</Button>} />

// GOOD:
<Card>
  <Card.Header>
    <GearIcon />
    <Card.Title>Settings</Card.Title>
  </Card.Header>
  <Card.Body>{children}</Card.Body>
  <Card.Footer><Button>Save</Button></Card.Footer>
</Card>
```

## State Management with Zustand

```tsx
import { create } from 'zustand';

interface ProjectStore {
  projects: Project[];
  isLoading: boolean;
  fetchProjects: () => Promise<void>;
  addProject: (project: Project) => void;
}

export const useProjectStore = create<ProjectStore>((set) => ({
  projects: [],
  isLoading: false,
  fetchProjects: async () => {
    set({ isLoading: true });
    const projects = await api.getProjects();
    set({ projects, isLoading: false });
  },
  addProject: (project) => set((state) => ({ projects: [...state.projects, project] })),
}));

// Use selectors to prevent unnecessary re-renders
const projects = useProjectStore((s) => s.projects);
const isLoading = useProjectStore((s) => s.isLoading);
```

## Context - Use for Truly Global, Rarely Changing Data

```tsx
// Theme, locale, auth user - good for Context
const AuthContext = createContext<AuthContextValue | null>(null);

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) throw new Error('useAuth must be used within AuthProvider');
  return context;
}
```

## Error Boundaries

```tsx
import { Component, type ReactNode } from 'react';

class ErrorBoundary extends Component<
  { children: ReactNode; fallback: ReactNode },
  { hasError: boolean }
> {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    reportError(error, info.componentStack);
  }

  render() {
    if (this.state.hasError) return this.props.fallback;
    return this.props.children;
  }
}

// Usage: wrap feature boundaries, not the whole app
<ErrorBoundary fallback={<p>Something went wrong in the dashboard.</p>}>
  <Dashboard />
</ErrorBoundary>
```

## Performance Optimization

```tsx
// 1. Memoize expensive child components
const MemoizedList = React.memo(ItemList);

// 2. Lazy load routes and heavy components
const Settings = React.lazy(() => import('./pages/Settings'));

// 3. Virtualize long lists
import { useVirtualizer } from '@tanstack/react-virtual';

// 4. Split context to avoid re-renders
// BAD: one giant context with everything
// GOOD: separate AuthContext, ThemeContext, NotificationContext
```

## Anti-Patterns

- NEVER use `useEffect` to sync state that can be derived. Compute it during render.
- NEVER use `index` as `key` in lists that reorder, filter, or mutate.
- NEVER define components inside other components. Extract them to module scope.
- NEVER store derived state. If it can be computed from props or other state, compute it.
- NEVER use `any` as a prop type. Define explicit interfaces.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
