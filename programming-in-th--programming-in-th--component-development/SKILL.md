---
name: component-development
description: Use when creating or modifying React components. Ensures proper Server/Client component patterns, performance optimization, and accessibility.
metadata:
  author: programming-in-th
---

See [React patterns](../../docs/react-patterns.md) for full reference.

**Quick rules**:
- Default to Server Components (no directive)
- Use `'use client'` only for: `onClick`, `useState`, `useEffect`, browser APIs
- Push client boundary to smallest component
- Tailwind only, use `dark:` variants

**Example**:
```tsx
// Server Component (default)
export function TaskCard({ task }: { task: Task }) {
  return <div className="p-4 dark:bg-gray-800">{task.title}</div>
}
```

**Checklist**: Minimal client components, `dark:` variants, accessible inputs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/programming-in-th) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
