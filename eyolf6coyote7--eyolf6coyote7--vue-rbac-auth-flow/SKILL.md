---
name: vue-rbac-auth-flow
description: Wire Pinia auth store + Vue Router beforeEach guard + isMock auto-login + localStorage token persistence, matching the admin-dashboard/employee-portal pattern. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:
- add login / logout / route protection to a Vue 3 SPA
- gate a new route behind authentication
- mentions `useAuthStore`, `defineStore('auth', ...)`, `router.beforeEach`, `meta.public`, `VITE_MOCK`
- bypass login in the demo build

## Context

Both `admin-dashboard` and `employee-portal` ship the SAME minimal auth stack:

1. **Pinia store** at `src/stores/auth.ts` exposing `token` (persisted in `localStorage` under `admin_token` / `portal_token`), `user`, `isAuthenticated` computed, `login(username, password)`, `logout()`.
2. **Router guard** at `src/router/index.ts` reading `import.meta.env.VITE_MOCK === 'true'` and auto-calling `auth.login('admin', 'admin')` when in mock mode + not yet authenticated.
3. **`meta: { public: true }`** on the Login route only — every other route requires auth.
4. **Real client** (`api/real-client.ts`) attaches `Authorization: Bearer ${token}` via an axios interceptor reading the same localStorage key.

The mock-mode auto-login is the demo gate: `pnpm dev` with `VITE_MOCK=true` skips the login form entirely so reviewers land on the dashboard directly.

## Operating instructions

1. If the project doesn't have `src/stores/auth.ts`, create it copying `admin-dashboard/src/stores/auth.ts:1-23` verbatim and rename the localStorage key to `<project>_token`.
2. Open `src/router/index.ts` and ensure ONLY the `/login` route has `meta: { public: true }`.
3. Add the `beforeEach` guard reading `isMock` and auto-login (lines 52-62 of admin-dashboard).
4. Wire axios interceptor in `src/api/real-client.ts` to read the same localStorage key and attach the bearer header.
5. For new protected routes, simply add to `routes: [...]` — the default guard will protect them. NEVER add `meta: { public: true }` unless the route is the login page itself.
6. For role-gated routes, extend `meta` with `meta: { roles: ['Admin'] }` and check `auth.user?.role` inside the guard — but only add this if the user actually requires RBAC; default is auth-only.

## Reusable prompts / code patterns

Pinia store (copy verbatim, rename localStorage key per project):
```ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useAuthStore = defineStore('auth', () => {
  const token = ref(localStorage.getItem('admin_token') || '')
  const user = ref<{ name: string; role: string } | null>(null)

  const isAuthenticated = computed(() => !!token.value)

  function login(username: string, _password: string) {
    token.value = 'mock-jwt-token'
    user.value = { name: username, role: 'Admin' }
    localStorage.setItem('admin_token', token.value)
  }

  function logout() {
    token.value = ''
    user.value = null
    localStorage.removeItem('admin_token')
  }

  return { token, user, isAuthenticated, login, logout }
})
```

Router guard (must include the `isMock` auto-login branch):
```ts
const isMock = import.meta.env.VITE_MOCK === 'true'

router.beforeEach(async (to) => {
  const auth = useAuthStore()
  if (isMock && !auth.isAuthenticated) {
    await auth.login('admin', 'admin')
  }
  if (!to.meta.public && !auth.isAuthenticated) {
    return '/login'
  }
})
```

Axios interceptor (in `real-client.ts`):
```ts
http.interceptors.request.use((config) => {
  const token = localStorage.getItem('admin_token')
  if (token) config.headers.Authorization = `Bearer ${token}`
  return config
})
```

## Anti-patterns

- Do NOT skip the `isMock` auto-login branch — without it, reviewers running the demo see a login wall instead of the app.
- Do NOT store the token in `sessionStorage` or in-memory — the project relies on `localStorage` so reloads keep state.
- Do NOT add per-page `onMounted` redirects to `/login`; the router guard owns that responsibility.
- Do NOT hardcode the token value in the real client — always read from `localStorage`.
- Do NOT change the default `meta` to `meta: { public: true }` — the guard contract is "auth required by default".

## References

- `admin-dashboard/src/stores/auth.ts:1-23` — full Pinia store
- `admin-dashboard/src/router/index.ts:52-62` — `beforeEach` with isMock auto-login
- `admin-dashboard/src/api/real-client.ts:9-13` — axios bearer interceptor
- `employee-portal/src/stores/auth.ts` — sibling implementation (same shape, different storage key)

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
