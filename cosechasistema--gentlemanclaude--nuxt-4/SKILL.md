---
name: nuxt-4
description: Nuxt 4 patterns, composables, server routes, data fetching. Use when this capability is needed.
metadata:
  author: cosechasistema
---
---
name: nuxt-4
description: >
  Nuxt 4 patterns, composables, server routes, data fetching.
  Trigger: When writing Nuxt code - pages, composables, server routes, useFetch, layouts.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "1.0"
---

## When to Use

- Creating Nuxt pages, layouts, components
- Writing composables or server routes (Nitro)
- Data fetching with useFetch, useAsyncData, $fetch
- Middleware (route or server)
- SEO with useSeoMeta/useHead
- State management with useState or Pinia

---

## File Structure (Nuxt 4 `app/` directory)

```
project/
├── app/                     # All app code
│   ├── components/          # Auto-imported components
│   ├── composables/         # Auto-imported composables
│   ├── layouts/             # Layouts (default.vue, dashboard.vue)
│   ├── middleware/           # Route middleware
│   ├── pages/               # File-based routing
│   ├── plugins/             # Nuxt plugins
│   ├── utils/               # Auto-imported utilities
│   └── app.vue              # Root component
├── server/                  # Nitro server
│   ├── api/                 # API routes (/api prefix)
│   ├── routes/              # Server routes (no prefix)
│   ├── middleware/           # Server middleware (use sparingly)
│   └── utils/               # Server utilities
├── shared/                  # Shared between client/server
│   ├── utils/
│   └── types/
├── public/                  # Static files
└── nuxt.config.ts
```

---

## Critical Patterns

### Data Fetching Decision Tree (CRITICAL)

```
Component setup, initial load?     → useFetch()
Custom async logic, multiple calls → useAsyncData()
Client-only (button, form submit)  → $fetch()
Don't block navigation?            → useLazyFetch() / useLazyAsyncData()
Need nested reactivity (forms)?    → useFetch('/api/x', { deep: true })
```

### useFetch (Primary — SSR + Client)

```typescript
// Basic — auto-generates key from URL
const { data, pending, error, refresh } = await useFetch('/api/users')

// With reactive params (auto-refetches)
const id = ref(1)
const { data } = await useFetch(`/api/users/${id}`)

// Pick fields (performance)
const { data } = await useFetch('/api/users', {
  pick: ['id', 'name', 'email']
})

// Transform
const { data } = await useFetch('/api/users', {
  transform: (users) => users.map(u => ({ ...u, fullName: `${u.first} ${u.last}` }))
})

// Deep reactivity (opt-in — only for forms/mutations)
const { data } = await useFetch('/api/complex', { deep: true })
```

**Nuxt 4: Returns shallowRef by default.** Use `deep: true` ONLY when mutating nested properties.

### useAsyncData (Custom async)

```typescript
// ALWAYS provide unique key
const { data } = await useAsyncData('user-with-posts', async () => {
  const user = await $fetch('/api/user')
  const posts = await $fetch('/api/posts')
  return { user, posts }
})
```

### $fetch (Client-only mutations)

```typescript
// ✅ In event handlers
async function handleSubmit() {
  await $fetch('/api/submit', { method: 'POST', body: formData })
}

// ❌ NEVER in component setup — causes double fetch
```

### $fetch Plugin (API Instance)

```typescript
// app/plugins/api.ts
export default defineNuxtPlugin(() => {
  const api = $fetch.create({
    baseURL: '/api',
    onRequest({ options }) {
      const token = useCookie('token')
      if (token.value) {
        options.headers = { ...options.headers, Authorization: `Bearer ${token.value}` }
      }
    },
    onResponseError({ response }) {
      if (response.status === 401) navigateTo('/login')
    }
  })

  return { provide: { api } }
})

// Usage: const { $api } = useNuxtApp()
```

---

## Server Routes (Nitro)

```typescript
// server/api/users.get.ts — GET /api/users
export default defineEventHandler(async (event) => {
  return await db.users.findMany()
})

// server/api/users/[id].get.ts — GET /api/users/:id
export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')
  const user = await db.users.findUnique({ where: { id } })
  if (!user) throw createError({ statusCode: 404, message: 'User not found' })
  return user
})

// server/api/users.post.ts — POST /api/users
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  return await db.users.create({ data: body })
})
```

### Route Rules (nuxt.config.ts)

```typescript
export default defineNuxtConfig({
  routeRules: {
    '/api/cached': { swr: 60 },
    '/api/static': { prerender: true },
    '/admin/**': { ssr: false },
  }
})
```

---

## Middleware

### Route Middleware (Vue layer)

```typescript
// app/middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const user = useState('user')
  if (!user.value) return navigateTo('/login')
})

// Usage in page
definePageMeta({ middleware: 'auth' })

// Global: app/middleware/auth.global.ts
```

### Server Middleware (Use sparingly — anti-pattern for most cases)

```typescript
// server/middleware/log.ts
export default defineEventHandler((event) => {
  console.log('Request:', event.path)
})
```

---

## State Management

### useState (Simple, SSR-safe)

```typescript
const counter = useState('counter', () => 0)
const user = useState<User>('user', () => ({ name: '', email: '' }))
```

### Pinia (Computed state, devtools, complex mutations)

```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  const user = ref<User | null>(null)
  const isAuthenticated = computed(() => !!user.value)

  async function login(credentials: Credentials) {
    const data = await $fetch('/api/login', { method: 'POST', body: credentials })
    user.value = data.user
  }

  return { user, isAuthenticated, login }
})
```

**Decision: Simple state (flags, cookies) → useState. Derived state, devtools → Pinia.**

---

## SEO

```typescript
// ✅ Type-safe, XSS-safe
useSeoMeta({
  title: 'My Page',
  description: 'Page description',
  ogTitle: 'OG Title',
  ogImage: 'https://example.com/image.jpg',
})

// useHead for non-SEO meta
useHead({
  link: [{ rel: 'icon', href: '/favicon.ico' }],
  titleTemplate: (title) => title ? `${title} - My Site` : 'My Site'
})
```

---

## Error Handling

```typescript
// Server: throw errors
throw createError({ statusCode: 404, message: 'Not found' })

// Client: show error page
showError({ statusCode: 500, message: 'Something broke' })

// Component-level boundary
<NuxtErrorBoundary>
  <SomeComponent />
  <template #error="{ error, clearError }">
    <p>{{ error }}</p>
    <button @click="clearError">Retry</button>
  </template>
</NuxtErrorBoundary>

// Custom error page: app/error.vue
```

---

## Nuxt 4 Breaking Changes

1. **shallowRef default** — useFetch/useAsyncData return shallowRef (use `deep: true` for nested reactivity)
2. **Data cleared on refetch** — `data.value = null` during refresh (not stale data)
3. **`app/` directory** — Optional but recommended
4. **`shared/` directory** — Auto-imported between client and server

---

## Commands

```bash
nuxi dev                  # Dev server (localhost:3000)
nuxi build                # Production build
nuxi generate             # Static site generation
nuxi preview              # Preview production build
nuxi typecheck            # TypeScript check
nuxi prepare              # Generate types
nuxi cleanup              # Clean .nuxt, .output
nuxi analyze              # Bundle analysis
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cosechasistema) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
