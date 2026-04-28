---
name: supabase-patterns
description: Guides implementation of Next.js 15 App Router features with Supabase SSR. Helps choose between Server/Client Components, select correct Supabase client, and follow security patterns. Use when building pages, components, or API routes. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# Implementing Next.js with Supabase

Interactive guide for implementing features using patterns from `.claude/modules/nextjs-patterns.md` and `.claude/modules/supabase-security.md`.

## Quick Decision: Server or Client Component?

Use this decision tree to choose the right component type:

**Step 1: Does it need user interaction?**
- onClick, onChange, onSubmit handlers?
- useState, useEffect, or other React hooks?
- Browser APIs (localStorage, window)?

→ **YES** = Client Component (`'use client'`)
→ **NO** = Continue to Step 2

**Step 2: Does it fetch data from database/API?**
- Supabase queries?
- Fetch from external API?
- Read from database?

→ **YES** = Server Component (default)
→ **NO** = Server Component (default, unless Step 1 was yes)

## Common Scenarios

| What You're Building | Component Type | Supabase Client |
|---------------------|----------------|-----------------|
| Page that displays recipes | Server Component | `@/lib/supabase/server` |
| Save/delete button | Client Component | `@/lib/supabase/client` |
| Form with validation | Client Component | `@/lib/supabase/client` |
| Form with Server Action | Server Component | Use Server Action |
| Real-time chat | Client Component | `@/lib/supabase/client` |
| Dashboard with data | Server Component | `@/lib/supabase/server` |
| API route handler | Server (Route Handler) | `@/lib/supabase/server` |

## Implementation Patterns

### Pattern 1: Server Component with Data Fetching

**When:** Displaying data from database
**File location:** `app/**/page.tsx` or `app/**/layout.tsx`

```typescript
// app/recipes/page.tsx
import { createClient } from '@/lib/supabase/server'
import RecipeList from '@/components/RecipeList'

export default async function RecipesPage() {
  const supabase = await createClient()

  // Fetch data on server
  const { data: recipes, error } = await supabase
    .from('saved_recipes')
    .select('*')

  // Handle errors
  if (error) {
    return <ErrorDisplay message="Failed to load recipes" />
  }

  // Pass data to components
  return <RecipeList recipes={recipes} />
}
```

**See:** [nextjs-patterns.md](../../modules/nextjs-patterns.md#data-fetching-patterns)

### Pattern 2: Client Component with Interactivity

**When:** User interactions, state management
**File location:** `components/**/*.tsx`

```typescript
// components/SaveButton.tsx
'use client'

import { createClient } from '@/lib/supabase/client'
import { useState } from 'react'

export default function SaveButton({ recipeId }: { recipeId: string }) {
  const [saving, setSaving] = useState(false)
  const supabase = createClient()

  const handleSave = async () => {
    setSaving(true)
    const { error } = await supabase
      .from('saved_recipes')
      .insert({ id: recipeId })

    if (error) {
      console.error('Save failed:', error)
    }
    setSaving(false)
  }

  return (
    <button onClick={handleSave} disabled={saving}>
      {saving ? 'Saving...' : 'Save Recipe'}
    </button>
  )
}
```

**See:** [nextjs-patterns.md](../../modules/nextjs-patterns.md#client-component-interactivity)

### Pattern 3: Server Component → Client Component (Data Flow)

**When:** Need to pass server data to interactive components
**Rule:** Only pass required fields, never full objects

```typescript
// app/profile/page.tsx (Server Component)
import { createClient } from '@/lib/supabase/server'
import ProfileEditor from '@/components/ProfileEditor' // Client Component

export default async function ProfilePage() {
  const supabase = await createClient()
  const { data: { user } } = await supabase.auth.getUser()

  if (!user) {
    redirect('/login')
  }

  // ✅ CORRECT: Pass only required fields
  return (
    <ProfileEditor
      userId={user.id}
      email={user.email}
      name={user.user_metadata?.name}
    />
  )

  // ❌ WRONG: Don't pass full user object (security risk)
  // return <ProfileEditor user={user} />
}
```

**See:** [supabase-security.md](../../modules/supabase-security.md#data-security-patterns)

### Pattern 4: Server Action for Mutations

**When:** Form submissions, data mutations
**File location:** `app/actions.ts` or colocated with page

```typescript
// app/actions.ts
'use server'

import { createClient } from '@/lib/supabase/server'
import { revalidatePath } from 'next/cache'

export async function saveRecipe(formData: FormData) {
  const supabase = await createClient()

  // Validate user
  const { data: { user } } = await supabase.auth.getUser()
  if (!user) {
    return { error: 'Unauthorized' }
  }

  // Extract form data
  const name = formData.get('name') as string
  const ingredients = formData.get('ingredients') as string

  // Perform mutation
  const { error } = await supabase
    .from('saved_recipes')
    .insert({ name, ingredients, user_id: user.id })

  if (error) {
    return { error: error.message }
  }

  // Revalidate cache
  revalidatePath('/recipes')
  return { success: true }
}
```

**See:** [nextjs-patterns.md](../../modules/nextjs-patterns.md#server-actions)

## Which Supabase Client?

**Critical rule:** Use the correct client for the environment.

| Environment | Import | Usage |
|------------|--------|-------|
| Server Component | `import { createClient } from '@/lib/supabase/server'` | `const supabase = await createClient()` |
| Client Component | `import { createClient } from '@/lib/supabase/client'` | `const supabase = createClient()` |
| Server Action | `import { createClient } from '@/lib/supabase/server'` | `const supabase = await createClient()` |
| Route Handler | `import { createClient } from '@/lib/supabase/server'` | `const supabase = await createClient()` |
| Middleware | `import { createClient } from '@/lib/supabase/middleware'` | `const { supabase } = createClient(request)` |

**See:** [supabase-security.md](../../modules/supabase-security.md#supabase-client-creation)

## Security Checklist

Before implementing, verify:

- [ ] Using correct Supabase client for environment?
- [ ] Server Component for data fetching (not Client)?
- [ ] Only passing required fields to Client Components?
- [ ] Using `getUser()` not `getSession()` for auth validation?
- [ ] No sensitive data exposed to client?
- [ ] RLS policies considered for data access?

**See:** [supabase-security.md](../../modules/supabase-security.md#critical-security-rules)

## Common Mistakes to Avoid

| ❌ Mistake | ✅ Fix |
|-----------|--------|
| Async Client Component | Use Server Component or `useEffect` |
| Wrong Supabase client | Check table above |
| Passing full user object | Pass only needed fields |
| `any` types | Use specific types from `lib/supabase/types.ts` |
| No error handling | Always check for errors |
| Missing loading states | Show spinner/skeleton |

**See:** [anti-patterns.md](../../modules/anti-patterns.md)

## 🔄 Workflow

> **Kaynak:** [Supabase SSR Guide (Next.js)](https://supabase.com/docs/guides/auth/server-side/nextjs) & [Next.js 15 App Router Security](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations#security)

### Aşama 1: Environment & Client Initialization
- [ ] **Client Selection**: İhtiyaca göre `@supabase/ssr` kullanarak Server Client (RSC/Actions) veya Browser Client (RCC) başlat.
- [ ] **Middleware Guard**: RLS (Row Level Security) ek olarak, Middleware üzerinde oturum kontrolü ve session refresh mekanizmasını kur.
- [ ] **Type Mapping**: `database.types.ts` dosyasını otomatik generate et ve Supabase instance'ına enjekte et.

### Aşama 2: Data Orchestration
- [ ] **Server-Side Fetching**: Veriyi en üst seviyede (Page/Layout) Server Component'te çek ve alt bileşenlere (RCC) minimum veri ile aktar.
- [ ] **Server Actions**: Form mutation'ları için `use server` direktifiyle güvenli aksiyonlar tanımla ve `revalidatePath` ile cache'i güncelle.
- [ ] **RLS Audit**: Veritabanı seviyesinde `auth.uid()` bazlı politikaların tüm tablolar için (SELECT/INSERT/UPDATE) aktif olduğunu doğrula.

### Aşama 3: Security & Real-time Ops
- [ ] **Auth Validation**: `getSession()` yerine her zaman daha güvenli olan `getUser()` metodunu tercih et.
- [ ] **Real-time Handling**: Değişiklikleri anlık yansıtmak için `supabase.channel()` üzerinden subscription'ları kur ve temizle (`unmount`).
- [ ] **Edge Functions**: Yoğun işlem gerektiren veya 3. parti API entegrasyonu (örn: Stripe/SendGrid) için Edge Functions kullan.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Service Role Key asla client-side kodda (RCC) kullanılıyor mu? |
| 2 | Form submit sonrası "Optimistic UI" güncellemeleri yapıldı mı? |
| 3 | `process.env` verileri hem local hem de üretim (Supabase Dashboard) ortamında uyumlu mu? |

---
*Supabase Patterns v2.0 - With Workflow*

## Getting Help

For detailed patterns and examples:
- **Next.js patterns:** See [nextjs-patterns.md](../../modules/nextjs-patterns.md)
- **Supabase security:** See [supabase-security.md](../../modules/supabase-security.md)
- **TypeScript types:** See [typescript-standards.md](../../modules/typescript-standards.md)
- **What not to do:** See [anti-patterns.md](../../modules/anti-patterns.md)

## Quick Reference

**Server Component template:**
```typescript
import { createClient } from '@/lib/supabase/server'

export default async function Page() {
  const supabase = await createClient()
  const { data, error } = await supabase.from('table').select('*')
  if (error) return <Error />
  return <Component data={data} />
}
```

**Client Component template:**
```typescript
'use client'
import { createClient } from '@/lib/supabase/client'
import { useState } from 'react'

export default function Component() {
  const [state, setState] = useState()
  const supabase = createClient()
  // Your interactive logic
  return <div onClick={handler}>...</div>
}
```

**Server Action template:**
```typescript
'use server'
import { createClient } from '@/lib/supabase/server'
import { revalidatePath } from 'next/cache'

export async function action(formData: FormData) {
  const supabase = await createClient()
  const { data: { user } } = await supabase.auth.getUser()
  if (!user) return { error: 'Unauthorized' }

  // Mutation logic
  revalidatePath('/path')
  return { success: true }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
