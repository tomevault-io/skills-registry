---
name: vue-tsx
description: Vue 3 Composition API, defineComponent + TSX, reactivity system, and built-in components. Use when writing Vue components with defineComponent/TSX, watchers, or using Transition/Teleport/Suspense/KeepAlive. Use when this capability is needed.
metadata:
  author: uphg
---

# Vue

> Based on Vue 3.5+. Always use Composition API with `defineComponent()` + `.tsx`.

## Preferences

- Prefer TypeScript over JavaScript
- Prefer `defineComponent()` + `.tsx` over `<script setup lang="ts">` + `.vue`
- For performance, prefer `shallowRef` over `ref` if deep reactivity is not needed
- Always use Composition API over Options API
- Discourage using Reactive Props Destructure

## Core

| Topic | Description | Reference |
|-------|-------------|-----------|
| defineComponent + TSX | `defineComponent`, props/emits declaration, render function / JSX, generics | [define-component-tsx](references/define-component-tsx.md) |
| Reactivity & Lifecycle | ref, shallowRef, computed, watch, watchEffect, effectScope, lifecycle hooks, composables | [core-new-apis](references/core-new-apis.md) |

## Features

| Topic | Description | Reference |
|-------|-------------|-----------|
| Built-in Components & Directives | Transition, Teleport, Suspense, KeepAlive, v-memo, custom directives | [advanced-patterns](references/advanced-patterns.md) |

## Quick Reference

### Component Template

```tsx
import { ref, computed, watch, onMounted, defineComponent } from 'vue'

export default defineComponent(
  (props: { title: string; count?: number }) => {
    const doubled = computed(() => (props.count ?? 0) * 2)

    watch(() => props.title, (newVal) => {
      console.log('Title changed:', newVal)
    })

    onMounted(() => {
      console.log('Component mounted')
    })

    return () => (
      <div>{props.title} - {doubled.value}</div>
    )
  },
  {
    props: ['title', 'count']
  }
)
```

### Key Imports

```ts
// Reactivity
import { ref, shallowRef, computed, reactive, readonly, toRef, toRefs, toValue } from 'vue'

// Watchers
import { watch, watchEffect, watchPostEffect, onWatcherCleanup } from 'vue'

// Lifecycle
import { onMounted, onUpdated, onUnmounted, onBeforeMount, onBeforeUpdate, onBeforeUnmount } from 'vue'

// Utilities
import { nextTick, defineComponent, defineAsyncComponent } from 'vue'
```

---
> Source: [uphg/skills](https://github.com/uphg/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-04 -->
