---
name: performance-audit
description: Use when diagnosing or fixing performance issues. Covers React rendering optimization, bundle analysis, lazy loading, database query optimization, caching, N+1 queries, and connection pooling.
metadata:
  author: canivel
---

# Performance Optimization

## React Rendering Optimization

### React.memo - Prevent Unnecessary Re-Renders

```tsx
// Only use memo when:
// 1. Component re-renders frequently with the same props
// 2. Component is expensive to render (large tree, heavy computation)

const ExpensiveList = React.memo(function ExpensiveList({ items }: { items: Item[] }) {
  return (
    <ul>
      {items.map(item => <li key={item.id}>{item.name}</li>)}
    </ul>
  );
});

// NEVER memo everything by default. Profile first, optimize second.
```

### useMemo - Cache Expensive Computations

```tsx
function Dashboard({ transactions }: { transactions: Transaction[] }) {
  // GOOD: expensive computation that depends on data
  const summary = useMemo(() => {
    return transactions.reduce((acc, t) => ({
      total: acc.total + t.amount,
      count: acc.count + 1,
      average: (acc.total + t.amount) / (acc.count + 1),
    }), { total: 0, count: 0, average: 0 });
  }, [transactions]);

  // BAD: trivial computation, useMemo adds overhead
  // const fullName = useMemo(() => `${first} ${last}`, [first, last]);

  return <SummaryCard data={summary} />;
}
```

### useCallback - Stable Function References

```tsx
function TodoList({ todos }: { todos: Todo[] }) {
  // Stable reference prevents MemoizedTodoItem from re-rendering
  const handleToggle = useCallback((id: string) => {
    setTodos(prev => prev.map(t => t.id === id ? { ...t, done: !t.done } : t));
  }, []);

  return todos.map(todo => (
    <MemoizedTodoItem key={todo.id} todo={todo} onToggle={handleToggle} />
  ));
}
```

### Key Rendering Rules

```tsx
// 1. Lift state up only as far as needed. Keep state close to where it is used.
// 2. Split components at render boundaries. If part of the UI changes often, isolate it.
// 3. Use children pattern to avoid re-rendering wrappers.

// BAD: Parent re-renders on every mouse move, re-rendering ExpensiveTree
function Parent() {
  const [pos, setPos] = useState({ x: 0, y: 0 });
  return (
    <div onMouseMove={e => setPos({ x: e.clientX, y: e.clientY })}>
      <Cursor pos={pos} />
      <ExpensiveTree />  {/* re-renders unnecessarily */}
    </div>
  );
}

// GOOD: Extract the changing part
function MouseTracker({ children }: { children: React.ReactNode }) {
  const [pos, setPos] = useState({ x: 0, y: 0 });
  return (
    <div onMouseMove={e => setPos({ x: e.clientX, y: e.clientY })}>
      <Cursor pos={pos} />
      {children}  {/* children reference is stable, no re-render */}
    </div>
  );
}
```

## Bundle Analysis

```bash
# Analyze bundle size
npx vite-bundle-visualizer   # Vite projects
npx @next/bundle-analyzer    # Next.js projects

# Check individual package sizes before adding
npx bundlephobia <package-name>
```

### Code Splitting and Lazy Loading

```tsx
// Route-level splitting
const Dashboard = React.lazy(() => import('./pages/Dashboard'));
const Settings = React.lazy(() => import('./pages/Settings'));

function App() {
  return (
    <Suspense fallback={<PageSkeleton />}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </Suspense>
  );
}

// Component-level splitting for heavy features
const Chart = React.lazy(() => import('./components/Chart'));
const MarkdownEditor = React.lazy(() => import('./components/MarkdownEditor'));
```

## Database Query Optimization

### N+1 Query Problem

```ts
// BAD: N+1 queries (1 query for projects + N queries for tasks)
const projects = await db.select().from(projectsTable);
for (const project of projects) {
  project.tasks = await db.select().from(tasksTable).where(eq(tasksTable.projectId, project.id));
}

// GOOD: Single query with join
const result = await db
  .select()
  .from(projectsTable)
  .leftJoin(tasksTable, eq(tasksTable.projectId, projectsTable.id))
  .where(eq(projectsTable.ownerId, userId));

// GOOD: Two queries with IN clause
const projects = await db.select().from(projectsTable).where(eq(projectsTable.ownerId, userId));
const projectIds = projects.map(p => p.id);
const tasks = await db.select().from(tasksTable).where(inArray(tasksTable.projectId, projectIds));
```

### Indexing for Query Performance

```ts
// Index columns used in WHERE, JOIN, and ORDER BY
pgTable('tasks', { ... }, (table) => ({
  projectIdx: index('tasks_project_id_idx').on(table.projectId),
  statusIdx: index('tasks_status_idx').on(table.status),
  // Composite index for common query patterns
  projectStatusIdx: index('tasks_project_status_idx').on(table.projectId, table.status),
}));

// Analyze slow queries
// EXPLAIN ANALYZE SELECT * FROM tasks WHERE project_id = 1 AND status = 'todo';
```

### Pagination

```ts
// Offset pagination (simple, suitable for small datasets)
const page = 1, limit = 20;
const data = await db.select().from(tasks)
  .limit(limit).offset((page - 1) * limit)
  .orderBy(desc(tasks.createdAt));

// Cursor pagination (better for large datasets)
const data = await db.select().from(tasks)
  .where(lt(tasks.createdAt, cursorDate))
  .limit(limit)
  .orderBy(desc(tasks.createdAt));
```

## Caching Strategies

```ts
// In-memory cache for hot data (small, read-heavy)
import { LRUCache } from 'lru-cache';

const userCache = new LRUCache<string, User>({ max: 1000, ttl: 5 * 60 * 1000 });

async function getUser(id: string): Promise<User> {
  const cached = userCache.get(id);
  if (cached) return cached;
  const user = await db.select().from(users).where(eq(users.id, id));
  userCache.set(id, user);
  return user;
}

// Redis for shared/distributed cache
async function getCachedProjects(userId: string): Promise<Project[]> {
  const cached = await redis.get(`projects:${userId}`);
  if (cached) return JSON.parse(cached);
  const projects = await db.select().from(projectsTable).where(eq(projectsTable.ownerId, userId));
  await redis.set(`projects:${userId}`, JSON.stringify(projects), 'EX', 300);
  return projects;
}

// Invalidate on write
async function createProject(data: NewProject) {
  const project = await db.insert(projectsTable).values(data).returning();
  await redis.del(`projects:${data.ownerId}`); // invalidate cache
  return project;
}
```

## Connection Pooling

```ts
// Always use connection pools, never single connections
import { Pool } from 'pg';

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 20,                    // max connections
  idleTimeoutMillis: 30000,   // close idle connections after 30s
  connectionTimeoutMillis: 5000,
});

// For serverless (Vercel, Lambda), use connection pooler like PgBouncer
// or Neon's connection pooling endpoint
```

## Anti-Patterns

- NEVER optimize without profiling first. Use React DevTools Profiler and database EXPLAIN.
- NEVER cache mutable data without an invalidation strategy.
- NEVER use `SELECT *` when you only need a few columns.
- NEVER skip pagination on list endpoints. Always limit results.
- NEVER open a new database connection per request. Always use a pool.
- NEVER add indexes blindly. Each index slows writes and consumes storage.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
