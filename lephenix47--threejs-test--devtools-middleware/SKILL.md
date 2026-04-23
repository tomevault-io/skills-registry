---
name: zustand-devtools-middleware
description: Use Zustand devtools middleware for time-travel debugging and state inspection. Use when this capability is needed.
metadata:
  author: lephenix47
---

# Zustand DevTools Middleware

## Pattern
Wrap stores with `devtools` middleware for debugging.

## ✅ With DevTools
```typescript
import { create } from "zustand";
import { devtools, persist } from "zustand/middleware";

export const useStore = create<State>()(
  devtools(
    persist(
      (set) => ({ /* ... */ }),
      { name: "storage-key" }
    ),
    { name: "MyStore" } // Shows as "MyStore" in DevTools
  )
);
```

## Middleware Order
```typescript
create<State>()(
  devtools(      // 1. Outermost - DevTools
    persist(     // 2. Middle - Persistence
      (set) => ({ /* store */ }),
      { /* persist config */ }
    ),
    { name: "StoreName" }
  )
);
```

## DevTools Features
- Time-travel debugging
- Inspect state changes
- Track action dispatches
- Export/import state snapshots

## When to Use
- ✅ Development builds
- ✅ Debugging complex state
- ✅ Tracking action flow
- ❌ Not needed in production (tree-shaken)

## Project Example
[useTranscriptionSettingsStore.ts:48-181](src/app/stores/useTranscriptionSettingsStore.ts#L48-L181)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lephenix47) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
