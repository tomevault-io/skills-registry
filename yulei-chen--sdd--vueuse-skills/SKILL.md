---
name: vueuse-skills
description: Uses VueUse (useStorage and related) for client-side state and localStorage. Use when persisting settings or using VueUse in this project. Use when this capability is needed.
metadata:
  author: yulei-chen
---

# VueUse in this project

## useStorage for localStorage

Use VueUse’s `useStorage` to persist settings in the browser:

```ts
import { useStorage } from '@vueuse/core'

const value = useStorage('storage-key', defaultValue, undefined, { initOnMounted: true })
```

- **Key**: Unique string (e.g. `sdd-currency`, `sdd-daily-pay`).
- **Default**: Initial value when the key is missing. For client-only defaults (e.g. from `navigator.language`), compute the default inside a function that runs only on client, or use `initOnMounted: true` so storage is read after mount.
- **Storage**: Omit or pass `undefined` for default `localStorage`; pass `sessionStorage` for session-only.
- **Options**: `initOnMounted: true` avoids SSR issues when reading/writing localStorage. Use `mergeDefaults: true` when the default is an object and you want to merge with existing stored keys.

In Nuxt, **always** use an explicit import: `import { useStorage } from '@vueuse/core'` (VueUse’s `useStorage` is not auto-imported in favor of Nitro’s).

## Other VueUse composables

Composables like `useIntervalFn` can be used for timers (e.g. refresh every 5 seconds). With `@vueuse/nuxt` many are auto-imported; use explicit import from `@vueuse/core` if needed.

## Reference

- [VueUse useStorage](https://vueuse.org/core/useStorage/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yulei-chen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
