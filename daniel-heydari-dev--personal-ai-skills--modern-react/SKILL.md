---
name: modern-react
description: Modern React patterns and best practices including hooks, composition, and performance. Use when building React components, managing state, writing custom hooks, or when user asks about "React hooks", "component patterns", "React performance", or "state management". Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Modern React

Write clean, performant React components following modern best practices.

## Component Structure

### Rules

- ✅ DO: Use functional components with hooks
- ✅ DO: Keep components small and focused
- ✅ DO: Co-locate related code (styles, tests, types)
- ❌ DON'T: Use class components for new code
- ❌ DON'T: Create god components that do everything

### File Structure

```
components/
  Button/
    Button.tsx        # Component
    Button.test.tsx   # Tests
    Button.module.css # Styles (if not using Tailwind)
    index.ts          # Re-export
```

### Example

```typescript
// ✅ Good - clean component structure
interface ButtonProps {
  variant?: 'primary' | 'secondary';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
  children: React.ReactNode;
  onClick?: () => void;
}

export function Button({
  variant = 'primary',
  size = 'md',
  isLoading = false,
  children,
  onClick,
}: ButtonProps) {
  return (
    <button
      className={cn(styles.button, styles[variant], styles[size])}
      onClick={onClick}
      disabled={isLoading}
    >
      {isLoading ? <Spinner /> : children}
    </button>
  );
}
```

## Hooks Best Practices

### Rules

- ✅ DO: Follow Rules of Hooks (top level, React functions only)
- ✅ DO: Extract custom hooks for reusable logic
- ✅ DO: Use the right hook for the job
- ❌ DON'T: Call hooks conditionally
- ❌ DON'T: Overuse useEffect

### When to Use Each Hook

| Hook          | Use Case                          |
| ------------- | --------------------------------- |
| `useState`    | Local component state             |
| `useReducer`  | Complex state logic               |
| `useContext`  | Access context values             |
| `useRef`      | Mutable values, DOM refs          |
| `useMemo`     | Expensive calculations            |
| `useCallback` | Stable function references        |
| `useEffect`   | Synchronize with external systems |

## Avoid useEffect Abuse

### Rules

- ✅ DO: Use for external system synchronization
- ✅ DO: Use for subscriptions (and clean up!)
- ❌ DON'T: Use for derived state (use `useMemo`)
- ❌ DON'T: Use for event handlers
- ❌ DON'T: Use for data that can be calculated during render

### Examples

```typescript
// ❌ Bad - derived state in useEffect
function SearchResults({ items, query }: Props) {
  const [filtered, setFiltered] = useState<Item[]>([]);

  useEffect(() => {
    setFiltered(items.filter(i => i.name.includes(query)));
  }, [items, query]);

  return <List items={filtered} />;
}

// ✅ Good - calculate during render
function SearchResults({ items, query }: Props) {
  const filtered = useMemo(
    () => items.filter(i => i.name.includes(query)),
    [items, query]
  );

  return <List items={filtered} />;
}

// ❌ Bad - resetting state on prop change
function Profile({ userId }: Props) {
  const [user, setUser] = useState<User | null>(null);

  useEffect(() => {
    setUser(null); // Reset
    fetchUser(userId).then(setUser);
  }, [userId]);
}

// ✅ Good - use key to reset component
function ProfilePage({ userId }: Props) {
  return <Profile key={userId} userId={userId} />;
}

// ✅ Good - legitimate useEffect for subscription
function ChatRoom({ roomId }: Props) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();

    return () => connection.disconnect(); // Cleanup!
  }, [roomId]);
}
```

## State Management

### Rules

- ✅ DO: Keep state as local as possible
- ✅ DO: Lift state only when needed
- ✅ DO: Use context for truly global state
- ✅ DO: Consider external stores for complex state (Zustand, Jotai)
- ❌ DON'T: Put everything in global state
- ❌ DON'T: Duplicate state (derive instead)

### State Colocation

```typescript
// ❌ Bad - state too high
function App() {
  const [searchQuery, setSearchQuery] = useState('');
  return <SearchPage query={searchQuery} setQuery={setSearchQuery} />;
}

// ✅ Good - state colocated
function SearchPage() {
  const [searchQuery, setSearchQuery] = useState('');
  return (
    <>
      <SearchInput value={searchQuery} onChange={setSearchQuery} />
      <SearchResults query={searchQuery} />
    </>
  );
}
```

## Composition Over Configuration

### Rules

- ✅ DO: Use composition (children, render props)
- ✅ DO: Create compound components for complex UI
- ❌ DON'T: Pass dozens of props for configuration

### Examples

```typescript
// ❌ Bad - configuration via props
<Card
  title="Hello"
  subtitle="World"
  showHeader={true}
  showFooter={true}
  footerContent={<Button>Click</Button>}
  headerAction={<IconButton icon="close" />}
/>

// ✅ Good - composition
<Card>
  <Card.Header>
    <Card.Title>Hello</Card.Title>
    <IconButton icon="close" />
  </Card.Header>
  <Card.Body>
    Content here
  </Card.Body>
  <Card.Footer>
    <Button>Click</Button>
  </Card.Footer>
</Card>
```

## Event Handling

### Rules

- ✅ DO: Handle events in event handlers, not useEffect
- ✅ DO: Use useCallback for handlers passed to memoized children
- ❌ DON'T: Create arrow functions in JSX for frequently re-rendered components

### Examples

```typescript
// ✅ Good - event in handler
function Form() {
  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    await saveData(formData);
    showToast('Saved!');
  };

  return <form onSubmit={handleSubmit}>...</form>;
}

// ❌ Bad - event response in useEffect
function Form() {
  const [submitted, setSubmitted] = useState(false);

  useEffect(() => {
    if (submitted) {
      saveData(formData);
      showToast('Saved!');
    }
  }, [submitted]);
}
```

## TypeScript with React

### Rules

- ✅ DO: Define prop interfaces
- ✅ DO: Use `React.ReactNode` for children
- ✅ DO: Use discriminated unions for component variants
- ❌ DON'T: Use `React.FC` (prefer explicit prop types)

### Examples

```typescript
// ✅ Good - explicit types
interface ButtonProps {
  children: React.ReactNode;
  onClick?: () => void;
}

function Button({ children, onClick }: ButtonProps) {
  return <button onClick={onClick}>{children}</button>;
}

// ✅ Good - discriminated union for variants
type AlertProps =
  | { variant: 'success'; onDismiss: () => void }
  | { variant: 'error'; onRetry: () => void }
  | { variant: 'info' };

function Alert(props: AlertProps) {
  switch (props.variant) {
    case 'success':
      return <div><button onClick={props.onDismiss}>Dismiss</button></div>;
    case 'error':
      return <div><button onClick={props.onRetry}>Retry</button></div>;
    case 'info':
      return <div>Info message</div>;
  }
}
```

## Custom Hooks

### Rules

- ✅ DO: Extract reusable logic into custom hooks
- ✅ DO: Prefix with "use"
- ✅ DO: Return consistent shapes (object for multiple values)
- ❌ DON'T: Extract too early (wait for duplication)

### Examples

```typescript
// ✅ Good - reusable data fetching hook
function useUser(userId: string) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    setIsLoading(true);
    fetchUser(userId)
      .then(setUser)
      .catch(setError)
      .finally(() => setIsLoading(false));
  }, [userId]);

  return { user, isLoading, error };
}

// Usage
function Profile({ userId }: { userId: string }) {
  const { user, isLoading, error } = useUser(userId);

  if (isLoading) return <Spinner />;
  if (error) return <Error error={error} />;
  return <UserCard user={user} />;
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
