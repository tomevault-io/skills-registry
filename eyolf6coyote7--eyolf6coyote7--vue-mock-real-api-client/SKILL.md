---
name: vue-mock-real-api-client
description: Add a typed API method to a Vue 3 project that switches between mock-client and real-client (axios) at module load time via VITE_MOCK, with shared TypeScript types. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:
- add a new API endpoint to `admin-dashboard` or `employee-portal`
- expose data to a new page that needs both demo (mock) and prod (axios) modes
- mentions `mockClient`, `realClient`, `VITE_MOCK`, `api.getXxx`, `delay()`
- introduce a new entity type that both clients must return

## Context

Both `admin-dashboard` and `employee-portal` use the same dual-client switch pattern:

```ts
// src/api/index.ts
const useMock = import.meta.env.VITE_MOCK === 'true'
export const api = useMock ? mockClient : realClient
export type { User, Template, AuditEntry, KpiData } from './mock-client'
```

The switch is made ONCE at module load — there is no runtime toggle. Types live in `mock-client.ts` and are re-exported from `index.ts`. The real client (`real-client.ts`) imports these types from `./mock-client` to guarantee shape parity.

The mock client wraps responses in a `delay<T>(data): Promise<T>` helper (200ms `setTimeout`) so the demo mode shows realistic loading states. The real client uses `axios` with `baseURL: '/api/v1'` and a JWT bearer interceptor.

## Operating instructions

When adding a new endpoint `getThings()`:

1. Open `src/api/mock-client.ts`. Add the `Thing` interface near the other type exports (around lines 1-32 of admin-dashboard's file).
2. Define `mockThings: Thing[] = [...]` with realistic-looking demo data (names, ISO dates, statuses) — at least 5-10 entries.
3. Add the method to the `mockClient` object: `getThings: () => delay([...mockThings])`. The spread is mandatory — never return the array reference.
4. For mutation methods, mutate the in-memory array AND `delay` the mutated entry: `createThing: (t) => delay({ ...t, id: nextId++ } as Thing)`.
5. Open `src/api/real-client.ts`. Import the type from `./mock-client`: `import type { Thing } from './mock-client'`.
6. Add the method to `realClient`: `getThings: () => http.get<Thing[]>('/things').then((r) => r.data)`.
7. Open `src/api/index.ts`. Re-export the type in the `export type {...}` line.
8. In Vue components, only ever import from `@/api`: `import { api, type Thing } from '@/api'`. NEVER import from `@/api/mock-client` or `@/api/real-client` directly.
9. If you add a unit test, mirror `src/api/mock-client.test.ts` and assert the mock returns within ~200ms via `vi.useFakeTimers()`.

## Reusable prompts / code patterns

Mock helper + entry point:
```ts
function delay<T>(data: T): Promise<T> {
  return new Promise((resolve) => setTimeout(() => resolve(data), 200))
}

export const mockClient = {
  getThings: () => delay([...mockThings]),
  createThing: (t: Omit<Thing, 'id'>) => delay({ ...t, id: mockThings.length + 1 } as Thing),
  updateThing: (id: number, t: Partial<Thing>) => {
    const idx = mockThings.findIndex((x) => x.id === id)
    if (idx >= 0) Object.assign(mockThings[idx], t)
    return delay(mockThings[idx])
  },
  deleteThing: (id: number) => delay(mockThings.filter((x) => x.id !== id)),
}
```

Real client mirror (axios wrapper):
```ts
import axios from 'axios'
import type { Thing } from './mock-client'

const http = axios.create({ baseURL: '/api/v1', timeout: 10000 })

http.interceptors.request.use((config) => {
  const token = localStorage.getItem('admin_token')
  if (token) config.headers.Authorization = `Bearer ${token}`
  return config
})

export const realClient = {
  getThings: () => http.get<Thing[]>('/things').then((r) => r.data),
  createThing: (t: Omit<Thing, 'id'>) => http.post<Thing>('/things', t).then((r) => r.data),
  updateThing: (id: number, t: Partial<Thing>) => http.put<Thing>(`/things/${id}`, t).then((r) => r.data),
  deleteThing: (id: number) => http.delete(`/things/${id}`).then((r) => r.data),
}
```

Switch entry (do not modify the export shape):
```ts
import { mockClient } from './mock-client'
import { realClient } from './real-client'
const useMock = import.meta.env.VITE_MOCK === 'true'
export const api = useMock ? mockClient : realClient
export type { User, Template, AuditEntry, KpiData, Thing } from './mock-client'
```

## Anti-patterns

- Do NOT add a runtime toggle (`api.useMock = true`) — the switch must happen ONCE at module load.
- Do NOT define types in `real-client.ts` — types live in `mock-client.ts` so the mock is the single source of truth.
- Do NOT return the raw mock array reference — always spread (`[...mockArray]`) so consumers can't mutate the source.
- Do NOT call `axios` directly in components — go through `realClient`.
- Do NOT skip the `delay()` wrapper in the mock client — the loading-state UX depends on it.
- Do NOT use a different `baseURL` per page; the entire app talks to `/api/v1` through one axios instance.

## References

- `admin-dashboard/src/api/index.ts:1-8` — switch + type re-export
- `admin-dashboard/src/api/mock-client.ts:200-216` — `delay()` helper + `mockClient` shape
- `admin-dashboard/src/api/real-client.ts:1-24` — axios instance + interceptor
- `admin-dashboard/src/api/mock-client.test.ts` — test pattern reference

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
