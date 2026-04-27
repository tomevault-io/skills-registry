---
name: vue-i18n-element-plus-bootstrap
description: Bootstrap a Vue 3 SPA with Pinia + Vue Router + vue-i18n + Element Plus + Element Plus icons + web vitals, matching admin-dashboard/main.ts. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:
- create a new Vue 3 sub-app inside the workspace
- add i18n / Element Plus / icons / Pinia to a fresh Vite scaffold
- mentions `createApp`, `createI18n`, `ElementPlus`, `ElementPlusIconsVue`, `initWebVitals`, `main.ts`
- add a new locale (e.g. `ja-JP`) to an existing app

## Context

Both `admin-dashboard/src/main.ts:1-33` and `employee-portal/src/main.ts` follow the SAME bootstrap recipe:

1. `createI18n` with `legacy: false`, `locale` from `localStorage.getItem('lang') || 'en'`, `fallbackLocale: 'en'`, messages loaded from JSON files in `src/i18n/`
2. `createApp(App)` then `.use(createPinia()).use(router).use(i18n).use(ElementPlus)`
3. Loop over `ElementPlusIconsVue` and `app.component(key, component)` to register every icon globally
4. `app.mount('#app')` then `initWebVitals()` (defined in `src/vitals.ts`) to start CLS/LCP/FID reporting

The order matters: Pinia BEFORE router (because the router guard reads from auth store), router BEFORE i18n (so guards can use i18n), i18n BEFORE Element Plus (so Element Plus can read locale).

Locale persistence: only the `locale` is read from `localStorage`; switching locale at runtime via `LanguageToggle` component must `localStorage.setItem('lang', newLang)` AND set `i18n.global.locale.value = newLang`.

## Operating instructions

1. Create `src/main.ts` with the imports listed in the pattern below — DO NOT change order.
2. Create `src/i18n/en.json` and `src/i18n/zh-TW.json` with matching key trees. Add new locales by creating `src/i18n/<lang>.json` and importing them in `main.ts`.
3. Create `src/vitals.ts` exporting `initWebVitals()` (use `web-vitals` package's `onCLS`, `onLCP`, `onFID`, `onINP`, `onTTFB`).
4. Use `legacy: false` ALWAYS (composition API mode). Code that does `useI18n()` requires this.
5. For every visible string in templates use `{{ $t('namespace.key') }}` or in scripts `const { t } = useI18n(); t('namespace.key')`. NEVER inline literal strings.
6. When adding a key, add it to ALL locale files in the same commit — missing keys fall back to English which is wrong for QA.
7. When adding new Element Plus icons, no extra step needed — the loop registers everything.

## Reusable prompts / code patterns

Bootstrap (copy verbatim, only swap App import / locale list):
```ts
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import { createI18n } from 'vue-i18n'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import * as ElementPlusIconsVue from '@element-plus/icons-vue'
import App from './App.vue'
import router from './router'
import { initWebVitals } from './vitals'
import en from './i18n/en.json'
import zhTW from './i18n/zh-TW.json'

const i18n = createI18n({
  legacy: false,
  locale: localStorage.getItem('lang') || 'en',
  fallbackLocale: 'en',
  messages: { en, 'zh-TW': zhTW },
})

const app = createApp(App)

app.use(createPinia())
app.use(router)
app.use(i18n)
app.use(ElementPlus)

for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
  app.component(key, component)
}

app.mount('#app')

initWebVitals()
```

Locale switcher pattern (in a component):
```ts
import { useI18n } from 'vue-i18n'
const { locale } = useI18n()
function setLang(lang: string) {
  locale.value = lang
  localStorage.setItem('lang', lang)
}
```

i18n usage in a component:
```vue
<script setup lang="ts">
import { useI18n } from 'vue-i18n'
const { t } = useI18n()
</script>
<template>
  <h1>{{ t('dashboard.title') }}</h1>
  <p>{{ $t('dashboard.subtitle') }}</p>
</template>
```

## Anti-patterns

- Do NOT use `legacy: true` — composition-API code (`useI18n()`) requires `legacy: false`.
- Do NOT register Element Plus icons individually — always use the `Object.entries(ElementPlusIconsVue)` loop.
- Do NOT skip `initWebVitals()` — the project tracks Lighthouse / web-vitals as part of CI.
- Do NOT call `app.use(router)` BEFORE `app.use(createPinia())` — router guards need Pinia stores ready first.
- Do NOT inline locale strings in components — every visible string MUST go through `t('namespace.key')`.
- Do NOT add a new locale by mutating `messages` after `createI18n` — pass it in the initial `messages` object.

## References

- `admin-dashboard/src/main.ts:1-33` — full bootstrap recipe
- `admin-dashboard/src/i18n/en.json` — locale JSON shape
- `admin-dashboard/src/vitals.ts` — web-vitals init
- `employee-portal/src/main.ts` — sibling app with identical pattern

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
