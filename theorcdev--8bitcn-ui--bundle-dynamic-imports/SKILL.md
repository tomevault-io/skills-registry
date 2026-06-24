---
name: bundle-dynamic-imports
description: Use next/dynamic for lazy-loading heavy components. Apply when importing large components like editors, charts, or rich text editors that aren't needed on initial render. Use when this capability is needed.
metadata:
  author: theorcdev
---

## Dynamic Imports for Heavy Components

Use `next/dynamic` to lazy-load large components not needed on initial render.

**Incorrect (Monaco bundles with main chunk ~300KB):**

```tsx
import { MonacoEditor } from './monaco-editor'

function CodePanel({ code }: { code: string }) {
  return <MonacoEditor value={code} />
}
```

**Correct (Monaco loads on demand):**

```tsx
import dynamic from 'next/dynamic'

const MonacoEditor = dynamic(
  () => import('./monaco-editor').then(m => m.MonacoEditor),
  { ssr: false }
)

function CodePanel({ code }: { code: string }) {
  return <MonacoEditor value={code} />
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theorcdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
