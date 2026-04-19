---
name: react-best-practices
description: Review, reference, and refactor React code using best practices Use when this capability is needed.
metadata:
  author: makifars
---

# React Best Practices

This skill reviews React code, provides best practice references, and suggests refactors.

## Usage modes:

- `/react-best-practices` - Review current file or component
- `/react-best-practices review` - Full code review against all rules
- `/react-best-practices rules` - Show relevant best practices
- `/react-best-practices refactor` - Apply improvements automatically

---

## Performance

### Avoid async waterfalls (CRITICAL)
```tsx
// Bad - sequential fetches (waterfall)
const user = await fetchUser(id);
const posts = await fetchPosts(user.id);
const comments = await fetchComments(posts[0].id);

// Good - parallel fetches when independent
const [user, settings, notifications] = await Promise.all([
  fetchUser(id),
  fetchSettings(id),
  fetchNotifications(id),
]);

// Good - start early, await late
const userPromise = fetchUser(id);
const settingsPromise = fetchSettings(id);
// ... do other work ...
const user = await userPromise;
const settings = await settingsPromise;
```

### Avoid barrel imports (CRITICAL)
```tsx
// Bad - imports entire barrel, bigger bundle
import { Button } from '@/components';
import { useAuth } from '@/hooks';

// Good - direct imports, tree-shaking works
import { Button } from '@/components/Button';
import { useAuth } from '@/hooks/useAuth';
```
Configure your bundler/linter to warn on barrel imports.

### Use dynamic imports for heavy components (CRITICAL)
```tsx
// Bad - loads immediately, blocks initial render
import { HeavyChart } from '@/components/HeavyChart';
import { PDFViewer } from '@/components/PDFViewer';

// Good - loads only when needed
import dynamic from 'next/dynamic'; // or React.lazy
const HeavyChart = dynamic(() => import('@/components/HeavyChart'));
const PDFViewer = dynamic(() => import('@/components/PDFViewer'), {
  loading: () => <Spinner />,
  ssr: false, // if client-only
});

// Good - preload on hover for perceived speed
const preloadChart = () => import('@/components/HeavyChart');
<button onMouseEnter={preloadChart} onClick={showChart}>
  View Chart
</button>
```

### Avoid inline functions in JSX
```tsx
// Bad
<button onClick={() => handleClick(id)}>Click</button>

// Good
const handleButtonClick = useCallback(() => handleClick(id), [id]);
<button onClick={handleButtonClick}>Click</button>

// Best (with React Compiler - no manual memoization needed)
<button onClick={() => handleClick(id)}>Click</button>
```

### Use React.lazy for code splitting
```tsx
const Dashboard = lazy(() => import('./pages/Dashboard'));
```

### Proper key usage
```tsx
// Bad - index as key
{items.map((item, index) => <Item key={index} />)}

// Good - unique stable id
{items.map((item) => <Item key={item.id} />)}
```

### Avoid unnecessary re-renders
- Use react-scan to detect re-renders in development
- Extract components that change independently
- Memoize expensive calculations with useMemo (or let React Compiler handle it)

---

## Hooks

### Proper dependency arrays
```tsx
// Bad - missing dependency
useEffect(() => {
  fetchUser(userId);
}, []);

// Good
useEffect(() => {
  fetchUser(userId);
}, [userId]);
```

### Extract custom hooks for reusable logic
```tsx
// Instead of repeating fetch logic, create a hook
function useUser(userId: string) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser(userId).then(setUser).finally(() => setLoading(false));
  }, [userId]);

  return { user, loading };
}
```

### Never call hooks conditionally
```tsx
// Bad
if (condition) {
  const [state, setState] = useState();
}

// Good
const [state, setState] = useState();
if (condition) {
  // use state
}
```

---

## State Management (Zustand)

### Store structure
```tsx
// src/store/userStore.ts
import { create } from 'zustand';

interface UserState {
  user: User | null;
  isLoading: boolean;
  // Actions
  setUser: (user: User) => void;
  logout: () => void;
  fetchUser: (id: string) => Promise<void>;
}

export const useUserStore = create<UserState>((set) => ({
  user: null,
  isLoading: false,
  setUser: (user) => set({ user }),
  logout: () => set({ user: null }),
  fetchUser: async (id) => {
    set({ isLoading: true });
    const user = await api.getUser(id);
    set({ user, isLoading: false });
  },
}));
```

### Select only what you need (avoid re-renders)
```tsx
// Bad - subscribes to entire store
const { user, posts, settings } = useUserStore();

// Good - subscribe to specific slice
const user = useUserStore((state) => state.user);
const logout = useUserStore((state) => state.logout);

// Good - multiple selectors with shallow compare
import { shallow } from 'zustand/shallow';
const { user, isLoading } = useUserStore(
  (state) => ({ user: state.user, isLoading: state.isLoading }),
  shallow
);
```

### Derived state with selectors
```tsx
// Keep store minimal, derive values
const completedTodos = useTodoStore(
  (state) => state.todos.filter((t) => t.completed)
);

// Or create reusable selectors
const selectCompletedTodos = (state: TodoState) =>
  state.todos.filter((t) => t.completed);

const completed = useTodoStore(selectCompletedTodos);
```

### Split stores by domain
```tsx
// Don't put everything in one store
// src/store/userStore.ts - user authentication
// src/store/cartStore.ts - shopping cart
// src/store/uiStore.ts - UI state (modals, sidebars)
```

### Persist state when needed
```tsx
import { persist } from 'zustand/middleware';

export const useCartStore = create<CartState>()(
  persist(
    (set) => ({
      items: [],
      addItem: (item) => set((state) => ({
        items: [...state.items, item]
      })),
    }),
    { name: 'cart-storage' } // localStorage key
  )
);
```

### Keep local state local
```tsx
// Not everything needs to be in Zustand
// Use useState for:
// - Form inputs
// - UI state (hover, focus)
// - Component-specific state

// Use Zustand for:
// - Shared state across components
// - Server cache (or use React Query)
// - State that persists across navigation
```

---

## Component Design

### Single Responsibility Principle
- Each component should do one thing well
- If a component file exceeds 200 lines, consider splitting

### Composition over prop drilling
```tsx
// Bad - passing props through multiple levels
<Parent user={user}>
  <Child user={user}>
    <GrandChild user={user} />
  </Child>
</Parent>

// Good - use composition
<Parent>
  <Child>
    <GrandChild user={user} />
  </Child>
</Parent>

// Good - use Zustand for shared state
const user = useUserStore((state) => state.user);
```

### Props interface naming
```tsx
// Consistent naming convention
interface ButtonProps {
  variant: 'primary' | 'secondary';
  children: React.ReactNode;
  onClick?: () => void;
}
```

---

## TypeScript

### Avoid `any` type
```tsx
// Bad
const handleData = (data: any) => {};

// Good
interface UserData {
  id: string;
  name: string;
}
const handleData = (data: UserData) => {};
```

### Use discriminated unions for state
```tsx
// Good - impossible states are impossible
type RequestState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };
```

### Proper event typing
```tsx
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};
```

---

## Testing

### Test behavior, not implementation
```tsx
// Bad - testing implementation details
expect(component.state.count).toBe(1);

// Good - testing user behavior
await user.click(screen.getByRole('button'));
expect(screen.getByText('Count: 1')).toBeInTheDocument();
```

### Use Testing Library queries properly
```tsx
// Priority (best to worst):
// 1. getByRole - accessible
// 2. getByLabelText - form fields
// 3. getByText - non-interactive
// 4. getByTestId - last resort
```

### Test edge cases
- Empty states
- Loading states
- Error states
- Boundary conditions

---

## Accessibility

### Use semantic HTML
```tsx
// Bad
<div onClick={handleClick}>Click me</div>

// Good
<button onClick={handleClick}>Click me</button>
```

### ARIA labels for icons/non-text elements
```tsx
<button aria-label="Close dialog">
  <CloseIcon />
</button>
```

### Keyboard navigation
- All interactive elements must be focusable
- Provide visible focus indicators
- Support Escape to close modals

### Color contrast
- Minimum 4.5:1 for normal text
- Minimum 3:1 for large text

---

## File Organization

### Co-locate related files
```
components/
└── Button/
    ├── Button.tsx        # Component
    ├── Button.test.tsx   # Tests
    ├── Button.styles.ts  # Styles (if not using Tailwind)
    └── index.ts          # Export
```

### Barrel exports
```tsx
// components/index.ts
export { Button } from './Button';
export { Input } from './Input';
```

---

## When reviewing code, check for:

### Critical (fix immediately)
1. [ ] No async waterfalls - use Promise.all for parallel fetches
2. [ ] No barrel imports - import directly from source
3. [ ] Heavy components use dynamic imports

### High Priority
4. [ ] No inline functions in JSX (unless using React Compiler)
5. [ ] Proper useEffect dependencies
6. [ ] No index as key in lists
7. [ ] TypeScript types are specific (no `any`)

### Standard
8. [ ] State is minimal, derived values are calculated
9. [ ] Components are focused and small
10. [ ] Tests cover user behavior
11. [ ] Accessible markup and ARIA labels
12. [ ] Consistent naming conventions
13. [ ] No prop drilling (use Zustand or composition)
14. [ ] Zustand selectors are specific (not destructuring entire store)
15. [ ] Stores are split by domain (not one giant store)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/makifars) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
