---
name: phase-6-ui-integration
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Phase 6: UI Integration

> Connect your frontend to the backend

## Key Tasks

### 1. API Client Setup

```typescript
// lib/api/client.ts
const API_BASE = process.env.NEXT_PUBLIC_API_URL;

export async function fetchAPI<T>(
  endpoint: string,
  options?: RequestInit
): Promise<T> {
  const res = await fetch(`${API_BASE}${endpoint}`, {
    headers: {
      'Content-Type': 'application/json',
      ...options?.headers,
    },
    ...options,
  });

  if (!res.ok) {
    throw new Error(`API Error: ${res.status}`);
  }

  return res.json();
}
```

### 2. Data Fetching

```typescript
// Server Component
async function UserList() {
  const users = await fetchAPI<User[]>('/api/users');

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### 3. Form Handling

```typescript
// Client Component with Server Action
'use client';

export function LoginForm() {
  const [pending, startTransition] = useTransition();

  async function handleSubmit(formData: FormData) {
    startTransition(async () => {
      await loginAction(formData);
    });
  }

  return (
    <form action={handleSubmit}>
      <input name="email" type="email" required />
      <input name="password" type="password" required />
      <button disabled={pending}>
        {pending ? 'Loading...' : 'Login'}
      </button>
    </form>
  );
}
```

### 4. State Management

- Server State: React Query / SWR
- Client State: Zustand / Jotai
- Form State: React Hook Form

## Integration Checklist

- [ ] API client configured
- [ ] Authentication flow working
- [ ] Error boundaries added
- [ ] Loading states implemented
- [ ] Optimistic updates where needed

## Next Phase

After completion: `/phase-7-seo-security` (Dynamic/Enterprise) or `/phase-9-deployment` (Starter)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
