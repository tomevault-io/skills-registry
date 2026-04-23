---
name: performance
description: Techniques for building fast applications Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Performance

Build fast, efficient applications with good user experience.

## Measuring First

### Rules

- ✅ DO: Measure before optimizing
- ✅ DO: Use profiling tools (Chrome DevTools, Lighthouse)
- ✅ DO: Set performance budgets
- ❌ DON'T: Optimize prematurely
- ❌ DON'T: Guess at bottlenecks

### Tools

- Chrome DevTools Performance tab
- Lighthouse for web vitals
- React DevTools Profiler
- Node.js `--prof` flag

## Bundle Size

### Rules

- ✅ DO: Analyze bundle with tools (webpack-bundle-analyzer)
- ✅ DO: Use tree-shakeable libraries
- ✅ DO: Code-split at route boundaries
- ✅ DO: Lazy load non-critical features
- ❌ DON'T: Import entire libraries for one function
- ❌ DON'T: Bundle dev dependencies

### Examples

```typescript
// ❌ Bad - imports entire lodash
import _ from 'lodash';
const result = _.debounce(fn, 300);

// ✅ Good - tree-shakeable import
import debounce from 'lodash/debounce';
const result = debounce(fn, 300);

// ✅ Better - use native or small library
function debounce<T extends (...args: unknown[]) => unknown>(
  fn: T,
  ms: number
): (...args: Parameters<T>) => void {
  let timeoutId: ReturnType<typeof setTimeout>;
  return (...args) => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn(...args), ms);
  };
}

// ✅ Good - dynamic import for code splitting
const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

## React Performance

### Rules

- ✅ DO: Use `React.memo` for expensive pure components
- ✅ DO: Use `useMemo` for expensive calculations
- ✅ DO: Use `useCallback` for callbacks passed to memoized children
- ✅ DO: Virtualize long lists (react-window, tanstack-virtual)
- ❌ DON'T: Memoize everything (has overhead)
- ❌ DON'T: Create objects/arrays in render

### Examples

```typescript
// ❌ Bad - creates new object every render
function Parent() {
  return <Child style={{ color: 'red' }} />;
}

// ✅ Good - stable reference
const childStyle = { color: 'red' };
function Parent() {
  return <Child style={childStyle} />;
}

// ✅ Good - memoize expensive component
const ExpensiveList = memo(function ExpensiveList({ items }: Props) {
  return items.map(item => <ExpensiveItem key={item.id} item={item} />);
});

// ✅ Good - memoize expensive calculation
function SearchResults({ items, query }: Props) {
  const filtered = useMemo(
    () => items.filter(item => item.name.includes(query)),
    [items, query]
  );
  return <List items={filtered} />;
}

// ✅ Good - stable callback for memoized child
function Parent({ items }: Props) {
  const handleClick = useCallback((id: string) => {
    console.log('clicked', id);
  }, []);

  return <MemoizedList items={items} onItemClick={handleClick} />;
}

// ✅ Good - virtualized list
import { useVirtualizer } from '@tanstack/react-virtual';

function VirtualList({ items }: { items: Item[] }) {
  const parentRef = useRef<HTMLDivElement>(null);

  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 50,
  });

  return (
    <div ref={parentRef} style={{ height: 400, overflow: 'auto' }}>
      <div style={{ height: virtualizer.getTotalSize() }}>
        {virtualizer.getVirtualItems().map(virtualRow => (
          <div
            key={virtualRow.key}
            style={{
              position: 'absolute',
              top: virtualRow.start,
              height: virtualRow.size,
            }}
          >
            {items[virtualRow.index].name}
          </div>
        ))}
      </div>
    </div>
  );
}
```

## Data Fetching

### Rules

- ✅ DO: Cache API responses
- ✅ DO: Use SWR/React Query for client-side caching
- ✅ DO: Prefetch data for likely navigations
- ✅ DO: Paginate large datasets
- ❌ DON'T: Fetch same data multiple times
- ❌ DON'T: Block render on non-critical data

### Examples

```typescript
// ✅ Good - React Query with caching
import { useQuery } from '@tanstack/react-query';

function UserProfile({ userId }: { userId: string }) {
  const { data, isLoading } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });

  if (isLoading) return <Skeleton />;
  return <Profile user={data} />;
}

// ✅ Good - prefetch on hover
function UserLink({ userId }: { userId: string }) {
  const queryClient = useQueryClient();

  const prefetch = () => {
    queryClient.prefetchQuery({
      queryKey: ['user', userId],
      queryFn: () => fetchUser(userId),
    });
  };

  return (
    <Link href={`/users/${userId}`} onMouseEnter={prefetch}>
      View Profile
    </Link>
  );
}
```

## Images & Media

### Rules

- ✅ DO: Use modern formats (WebP, AVIF)
- ✅ DO: Lazy load images below the fold
- ✅ DO: Serve responsive images with srcset
- ✅ DO: Use placeholder/blur-up technique
- ❌ DON'T: Load full-size images for thumbnails
- ❌ DON'T: Block page load with large images

### Examples

```typescript
// ✅ Good - Next.js Image optimization
import Image from 'next/image';

function Avatar({ src, name }: { src: string; name: string }) {
  return (
    <Image
      src={src}
      alt={name}
      width={50}
      height={50}
      placeholder="blur"
      blurDataURL={blurPlaceholder}
    />
  );
}

// ✅ Good - native lazy loading
<img src="photo.jpg" loading="lazy" alt="Description" />

// ✅ Good - responsive images
<img
  srcset="photo-320.jpg 320w, photo-640.jpg 640w, photo-1280.jpg 1280w"
  sizes="(max-width: 320px) 280px, (max-width: 640px) 600px, 1200px"
  src="photo-640.jpg"
  alt="Description"
/>
```

## Caching

### Rules

- ✅ DO: Set appropriate Cache-Control headers
- ✅ DO: Use ETags for validation
- ✅ DO: Cache at multiple levels (browser, CDN, server)
- ✅ DO: Use stale-while-revalidate pattern
- ❌ DON'T: Cache sensitive data inappropriately

### Examples

```typescript
// ✅ Good - cache headers
// Immutable assets (hashed filenames)
"Cache-Control: public, max-age=31536000, immutable";

// API responses
"Cache-Control: private, max-age=0, must-revalidate";

// Stale-while-revalidate
"Cache-Control: public, max-age=60, stale-while-revalidate=3600";
```

## Database Performance

### Rules

- ✅ DO: Index frequently queried columns
- ✅ DO: Use pagination for large result sets
- ✅ DO: Select only needed columns
- ✅ DO: Batch related queries
- ❌ DON'T: Use N+1 queries
- ❌ DON'T: SELECT \* in production

### Examples

```typescript
// ❌ Bad - N+1 problem
const users = await db.users.findMany();
for (const user of users) {
  user.posts = await db.posts.findMany({ where: { userId: user.id } });
}

// ✅ Good - include relation
const users = await db.users.findMany({
  include: { posts: true },
});

// ✅ Good - select only needed fields
const users = await db.users.findMany({
  select: { id: true, name: true, email: true },
});

// ✅ Good - pagination
const users = await db.users.findMany({
  skip: page * pageSize,
  take: pageSize,
  orderBy: { createdAt: "desc" },
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
