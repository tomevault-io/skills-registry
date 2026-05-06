---
name: nextjs
description: > Use when this capability is needed.
metadata:
  author: neversight
---

## Critical Patterns

### Server Components (DEFAULT)

```typescript
// ✅ ALWAYS: Server Components by default (no 'use client')
async function UserProfile({ userId }: { userId: string }) {
  const user = await db.user.findUnique({ where: { id: userId } });
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Client Components (WHEN NEEDED)

```typescript
// ✅ Add 'use client' only when needed
'use client';

import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

### Server Actions (REQUIRED)

```typescript
// ✅ ALWAYS: Use Server Actions for mutations
async function createUser(formData: FormData) {
  'use server';
  
  const name = formData.get('name') as string;
  await db.user.create({ data: { name } });
  revalidatePath('/users');
}

// In component
<form action={createUser}>
  <input name="name" />
  <button type="submit">Create</button>
</form>
```

---

## Decision Tree

```
Need interactivity?        → Add 'use client'
Need data fetching?        → Use Server Component
Need form mutation?        → Use Server Action
Need caching?              → Configure fetch cache
Need SEO?                  → Use generateMetadata
Need static pages?         → Use generateStaticParams
```

---

## Code Examples

### Data Fetching

```typescript
// app/users/page.tsx
async function UsersPage() {
  const users = await fetch('https://api.example.com/users', {
    next: { revalidate: 3600 }  // ISR: revalidate every hour
  }).then(res => res.json());
  
  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

### Metadata

```typescript
// app/users/[id]/page.tsx
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const user = await getUser(params.id);
  return {
    title: user.name,
    description: `Profile of ${user.name}`,
  };
}
```

---

## Commands

```bash
npx create-next-app@latest myapp
npm run dev
npm run build
npm run start
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
