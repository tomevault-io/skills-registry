---
name: nuxt-vue-patterns
description: > Use when this capability is needed.
metadata:
  author: PABERTHIER
---

Reference for Vue 3 + Nuxt 4 patterns used in Mellumine.

---

## SFC Structure (always in this order)

```vue
<template>
  <!-- markup -->
</template>

<script setup lang="ts">
// 1. Explicit imports (data utils and types — the only manual imports needed)
import { plushies } from '~/utils/data'
import type { Plushie } from '~/types/content'

// 2. Page meta
definePageMeta({ layout: 'default' })

// 3. Composables
const { t } = useI18n()

// 4. Reactive state / computed

// 5. Functions
</script>

<style lang="scss" scoped>
/* styles */
</style>
```

Rules:
- Always `<script setup lang="ts">` — never Options API, never `<script>` without `setup`
- Always `<style lang="scss" scoped>` — never plain CSS, never unscoped
- Template first, then script, then style

---

## Auto-Imports (NON-NEGOTIABLE: never import these manually)

Adding a manual import for any of these will cause lint errors:

**Vue reactivity:** `ref`, `computed`, `watch`, `watchEffect`, `reactive`, `readonly`, `toRef`, `toRefs`

**Nuxt composables:** `useI18n`, `useHead`, `useSeoMeta`, `useRoute`, `useRouter`, `useRuntimeConfig`, `useNuxtApp`, `useState`, `useFetch`, `useAsyncData`, `navigateTo`, `definePageMeta`, `useLocalePath`

**Components:** all components in `app/components/` are auto-imported — never import them in `<script setup>`

**VueUse composables:** all `@vueuse/nuxt` composables are auto-imported (`useIntersectionObserver`, `useMouse`, etc.)

---

## What Must Be Manually Imported

```typescript
// Data utilities — NOT auto-imported
import { plushies } from '~/utils/data'
import { socials, supports, credits } from '~/utils/data'

// Types always require explicit import
import type { Plushie } from '~/types/content'
import type { SocialLink } from '~/types/content'
import type { ImageSource } from '~/types/content'
```

---

## Internal Navigation

Always use `<NuxtLinkLocale>` for internal links — it automatically prepends the locale prefix:

```vue
<!-- ✅ Correct -->
<NuxtLinkLocale to="/about">À propos</NuxtLinkLocale>
<NuxtLinkLocale to="/plushies">Peluches</NuxtLinkLocale>

<!-- ❌ Wrong — do not use localePath() in this project -->
<NuxtLink :to="localePath('/about')">À propos</NuxtLink>
```

---

## CSS Custom Properties

The project uses CSS custom properties for theming — **not SCSS variables**:

```scss
<style lang="scss" scoped>
.my-element {
  color: var(--aurora-1);            /* ✅ CSS variable */
  font-family: var(--font-display);  /* ✅ CSS variable */
  background: rgba(124, 92, 255, 0.15);
}
</style>
```

**Key CSS variables:**

| Variable | Description |
|---|---|
| `--aurora-1` | Purple (#7c5cff) |
| `--aurora-2` | Pink (#ff8fd1) |
| `--aurora-3` | Cyan (#5be0ff) |
| `--font-display` | Cormorant Garamond (decorative) |
| `--font-body` | Quicksand (body text) |

---

## Reactive i18n in meta tags (NON-NEGOTIABLE)

Inside `useHead()` and `useSeoMeta()`, **all** `t()` calls must be wrapped in `computed()`:

```typescript
// ✅ Reactive — updates when locale changes
content: computed(() => t('pages.plushies.meta.content'))

// ❌ Wrong — stale on locale switch
content: t('pages.plushies.meta.content')
```

Exception: `articleTag` array values resolve with `.value`:
```typescript
articleTag: [
  computed(() => t('miscellaneous.plushies')).value,  // ✅ resolved string
]
```

---

## useReveal() Composable

For scroll-in animations, use the `useReveal()` composable:

```typescript
const { el: sectionEl, visible: sectionVisible } = useReveal()
```

In the template:
```vue
<section
  ref="sectionEl"
  :class="['section', 'fade-in-up', { 'is-visible': sectionVisible }]">
  ...
</section>
```

The `fade-in-up` and `is-visible` classes are defined in `main.css`.

---

## TypeScript Patterns

```typescript
// Always explicitly type reactive arrays
const items: Plushie[] = plushies

// Computed with type inference
const baseUrl = ref(runtimeConfig.public.i18n.baseUrl)
const canonicalUrl = computed(() => `${baseUrl.value}${route.path}`)
```

`strict: true` is enabled in `tsconfig.json` — no implicit `any`.

---

## Image Best Practices

```vue
<!-- Above-the-fold (hero) image -->
<img
  src="/images/mellumine/mellumine-look.webp"
  :alt="$t('pictures.mellumine_look.alt')"
  fetchpriority="high"
  decoding="async"
  width="640"
  height="900" />

<!-- Below-the-fold image -->
<img
  src="/images/mellumine/mellumine-1.webp"
  :alt="$t('pictures.mellumine_1.alt')"
  loading="lazy"
  decoding="async"
  width="600"
  height="800" />
```

- All images must be **WebP** format
- Always set `width` and `height` attributes to prevent layout shift
- Use `fetchpriority="high"` on hero images (above the fold)
- Use `loading="lazy"` on all other images
- Always bind `:alt` to an i18n key — never hardcode alt text

---

## Linting

```bash
yarn lint        # check
yarn lint:fix    # auto-fix
```

Rules to be aware of:
- `no-console: warn` — remove `console.log` before committing
- Prettier is integrated — formatting is enforced automatically

---
> Source: [PABERTHIER/mellumine](https://github.com/PABERTHIER/mellumine) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-04 -->
