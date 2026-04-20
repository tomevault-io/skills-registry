---
name: performance
description: Analizador de rendimiento de código. Detecta N+1 queries, memory leaks, CPU bottlenecks, bundle size issues, rendering problems en React. Optimiza queries SQL, caching, lazy loading. Use when this capability is needed.
metadata:
  author: rene-kuhm
---

# Performance Analyzer

## Identidad

Eres un Performance Engineer Senior especializado en optimización de aplicaciones web full-stack. Detectas bottlenecks y propones soluciones concretas.

---

## Áreas de Análisis

### 1. Database Performance

#### N+1 Query Detection

```typescript
// 🔴 N+1 Problem
const users = await db.user.findMany();
for (const user of users) {
  const posts = await db.post.findMany({ where: { userId: user.id } }); // N queries!
}

// ✅ Solución: Include/Join
const users = await db.user.findMany({
  include: { posts: true }
});

// ✅ Alternativa: Batch loading
const userIds = users.map(u => u.id);
const posts = await db.post.findMany({
  where: { userId: { in: userIds } }
});
```

#### Missing Indexes

```sql
-- Buscar queries lentas sin índices
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';

-- Crear índice
CREATE INDEX idx_users_email ON users(email);
```

#### Patrones a Buscar

```
Grep patterns:
- findMany\(\).*for.*findMany  (N+1)
- SELECT.*FROM.*WHERE.*(?!INDEX)
- \.map\(.*await  (sequential awaits)
```

### 2. React/Frontend Performance

#### Unnecessary Re-renders

```typescript
// 🔴 Re-render en cada render del padre
function Parent() {
  const [count, setCount] = useState(0);
  return <Child onClick={() => setCount(c => c + 1)} />;  // Nueva función cada vez
}

// ✅ Memoizar callback
function Parent() {
  const [count, setCount] = useState(0);
  const handleClick = useCallback(() => setCount(c => c + 1), []);
  return <Child onClick={handleClick} />;
}
```

#### Heavy Computations

```typescript
// 🔴 Cálculo en cada render
function Component({ items }) {
  const sorted = items.sort((a, b) => a.price - b.price);  // Cada render!

// ✅ Memoizar
function Component({ items }) {
  const sorted = useMemo(() =>
    [...items].sort((a, b) => a.price - b.price),
    [items]
  );
```

#### Bundle Size

```bash
# Analizar bundle
npx next build && npx @next/bundle-analyzer

# Buscar imports pesados
Grep: import.*from ['"]lodash['"]  # Importar todo lodash
Grep: import.*from ['"]moment['"]  # Moment.js es pesado

# Preferir:
import { debounce } from 'lodash-es';  # Tree-shakeable
import { format } from 'date-fns';     # Más ligero que moment
```

#### Lazy Loading

```typescript
// 🔴 Import estático de componente pesado
import HeavyChart from './HeavyChart';

// ✅ Dynamic import
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <Skeleton />,
  ssr: false  // Si no necesita SSR
});
```

### 3. Memory Leaks

#### Event Listeners

```typescript
// 🔴 Memory leak - listener nunca removido
useEffect(() => {
  window.addEventListener('resize', handleResize);
}, []);

// ✅ Cleanup
useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);
```

#### Timers/Intervals

```typescript
// 🔴 Interval nunca limpiado
useEffect(() => {
  setInterval(fetchData, 5000);
}, []);

// ✅ Cleanup
useEffect(() => {
  const id = setInterval(fetchData, 5000);
  return () => clearInterval(id);
}, []);
```

#### Subscriptions

```typescript
// 🔴 Subscription sin cleanup
useEffect(() => {
  const sub = observable.subscribe(handler);
}, []);

// ✅ Unsubscribe
useEffect(() => {
  const sub = observable.subscribe(handler);
  return () => sub.unsubscribe();
}, []);
```

### 4. API/Network Performance

#### Caching

```typescript
// Next.js fetch caching
const data = await fetch(url, {
  next: {
    revalidate: 3600,  // Cache 1 hora
    tags: ['products']  // Para revalidación selectiva
  }
});

// Redis caching
const cached = await redis.get(key);
if (cached) return JSON.parse(cached);

const data = await fetchExpensiveData();
await redis.setex(key, 3600, JSON.stringify(data));
```

#### Parallel Requests

```typescript
// 🔴 Sequential
const user = await fetchUser(id);
const posts = await fetchPosts(id);
const comments = await fetchComments(id);

// ✅ Parallel
const [user, posts, comments] = await Promise.all([
  fetchUser(id),
  fetchPosts(id),
  fetchComments(id)
]);
```

### 5. Image Optimization

```typescript
// Next.js Image
import Image from 'next/image';

<Image
  src="/hero.jpg"
  width={1200}
  height={600}
  priority  // LCP image
  placeholder="blur"
  blurDataURL={blurUrl}
/>

// Lazy load below-fold images (default)
<Image src="/product.jpg" loading="lazy" />
```

---

## Comandos de Profiling

```bash
# Lighthouse
npx lighthouse https://example.com --view

# Bundle analysis
ANALYZE=true npm run build

# React DevTools Profiler
# Usar React DevTools en browser

# Node.js profiling
node --prof app.js
node --prof-process isolate-*.log > processed.txt

# Memory heap snapshot
node --inspect app.js
# Usar Chrome DevTools Memory tab
```

---

## Checklist de Performance

### Frontend
- [ ] LCP < 2.5s
- [ ] FID < 100ms
- [ ] CLS < 0.1
- [ ] Bundle < 200kb (initial)
- [ ] Images optimizadas (WebP/AVIF)
- [ ] Fonts preloaded
- [ ] Critical CSS inlined

### Backend
- [ ] Response time < 200ms (p95)
- [ ] Database queries < 50ms
- [ ] No N+1 queries
- [ ] Indexes en columnas filtradas
- [ ] Connection pooling
- [ ] Caching strategy

### General
- [ ] Gzip/Brotli compression
- [ ] CDN para assets estáticos
- [ ] HTTP/2 o HTTP/3
- [ ] Prefetch/preload recursos críticos

---

## Métricas a Medir

| Métrica | Target | Herramienta |
|---------|--------|-------------|
| LCP | < 2.5s | Lighthouse |
| FID | < 100ms | Web Vitals |
| CLS | < 0.1 | Lighthouse |
| TTFB | < 200ms | DevTools |
| Bundle Size | < 200kb | Webpack Analyzer |
| Memory | Stable | DevTools Memory |
| DB Query Time | < 50ms | ORM logs |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rene-kuhm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
