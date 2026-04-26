---
name: frontend-react-performance-optimization
description: Standardized guidelines and patterns for Frontend React Performance Optimization. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend React Performance Optimization

## When to use this skill
- Application is slow or laggy
- Large lists causing performance issues
- Unnecessary re-renders
- Bundle size is too large
- Improving Core Web Vitals

## Workflow
- [ ] Profile with React DevTools Profiler
- [ ] Identify performance bottlenecks
- [ ] Apply appropriate optimization technique
- [ ] Measure improvement
- [ ] Avoid premature optimization

## Instructions

### React.memo

Prevent unnecessary re-renders of components:

```typescript
// Without memo - re-renders on every parent update
export const ExpensiveComponent = ({ data }: { data: string }) => {
  console.log('Rendering ExpensiveComponent');
  return <div>{data}</div>;
};

// With memo - only re-renders when data changes
export const ExpensiveComponent = React.memo(({ data }: { data: string }) => {
  console.log('Rendering ExpensiveComponent');
  return <div>{data}</div>;
});

// Custom comparison function
export const UserCard = React.memo(
  ({ user }: { user: User }) => <div>{user.name}</div>,
  (prevProps, nextProps) => {
    // Only re-render if user.id changed
    return prevProps.user.id === nextProps.user.id;
  }
);
```

### useMemo

Memoize expensive calculations:

```typescript
function ProductList({ products, searchTerm }: Props) {
  // ❌ Bad - filters on every render
  const filtered = products.filter(p => 
    p.name.includes(searchTerm)
  );

  // ✅ Good - only recalculates when dependencies change
  const filteredProducts = useMemo(() => {
    console.log('Filtering products');
    return products.filter(p => p.name.includes(searchTerm));
  }, [products, searchTerm]);

  return <div>{filteredProducts.map(...)}</div>;
}
```

### useCallback

Memoize function references:

```typescript
function Parent() {
  const [count, setCount] = useState(0);
  const [other, setOther] = useState(0);

  // ❌ Bad - new function on every render
  const handleClick = () => {
    setCount(count + 1);
  };

  // ✅ Good - same function reference
  const handleClick = useCallback(() => {
    setCount(c => c + 1);
  }, []); // Empty deps because we use functional update

  return <ExpensiveChild onClick={handleClick} />;
}

const ExpensiveChild = React.memo(({ onClick }: { onClick: () => void }) => {
  console.log('Child rendered');
  return <button onClick={onClick}>Click</button>;
});
```

### Code Splitting

**Route-based splitting:**
```typescript
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </Suspense>
  );
}
```

**Component-based splitting:**
```typescript
const HeavyChart = lazy(() => import('./components/HeavyChart'));

function Analytics() {
  const [showChart, setShowChart] = useState(false);

  return (
    <div>
      <button onClick={() => setShowChart(true)}>Show Chart</button>
      {showChart && (
        <Suspense fallback={<Skeleton />}>
          <HeavyChart data={data} />
        </Suspense>
      )}
    </div>
  );
}
```

### Virtualization

For long lists, render only visible items:

```typescript
import { FixedSizeList } from 'react-window';

function VirtualizedList({ items }: { items: string[] }) {
  const Row = ({ index, style }: { index: number; style: React.CSSProperties }) => (
    <div style={style}>
      {items[index]}
    </div>
  );

  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {Row}
    </FixedSizeList>
  );
}
```

### Debouncing & Throttling

```typescript
// Debounce: Wait for pause in events
function SearchInput() {
  const [search, setSearch] = useState('');

  const debouncedSearch = useMemo(
    () => debounce((value: string) => {
      // API call
      fetchResults(value);
    }, 500),
    []
  );

  useEffect(() => {
    debouncedSearch(search);
  }, [search, debouncedSearch]);

  return <input value={search} onChange={e => setSearch(e.target.value)} />;
}

// Throttle: Limit frequency
function ScrollHandler() {
  const handleScroll = useCallback(
    throttle(() => {
      console.log('Scroll position:', window.scrollY);
    }, 200),
    []
  );

  useEffect(() => {
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, [handleScroll]);

  return <div>Scroll me</div>;
}
```

### Lazy State Initialization

```typescript
// ❌ Bad - runs expensive function on every render
const [data, setData] = useState(expensiveComputation());

// ✅ Good - only runs once
const [data, setData] = useState(() => expensiveComputation());

// Example
const [user, setUser] = useState(() => {
  const stored = localStorage.getItem('user');
  return stored ? JSON.parse(stored) : null;
});
```

### Key Optimization

```typescript
// ❌ Bad - using index as key
{items.map((item, index) => (
  <Item key={index} data={item} />
))}

// ✅ Good - using stable unique identifier
{items.map(item => (
  <Item key={item.id} data={item} />
))}
```

### useTransition (React 18+)

Mark non-urgent updates:

```typescript
function SearchResults() {
  const [isPending, startTransition] = useTransition();
  const [search, setSearch] = useState('');
  const [results, setResults] = useState([]);

  const handleSearch = (value: string) => {
    setSearch(value); // Urgent: update input immediately

    startTransition(() => {
      // Non-urgent: filter can be delayed
      setResults(filterResults(value));
    });
  };

  return (
    <>
      <input value={search} onChange={e => handleSearch(e.target.value)} />
      {isPending ? <Spinner /> : <ResultsList results={results} />}
    </>
  );
}
```

### Profiling Checklist

- [ ] Use React DevTools Profiler
- [ ] Check "Highlight updates"
- [ ] Measure before optimizing
- [ ] Profile in production mode
- [ ] Use Chrome Lighthouse

## Resources
- Don't optimize prematurely
- Measure first, optimize second
- `memo` for expensive components
- `useMemo` for expensive calculations
- `useCallback` for stable references
- Code split routes and heavy components

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
