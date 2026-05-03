---
name: vue
description: Use when writing Vue SFCs, using ref/computed/watch, script setup, defineProps, defineEmits, defineModel, or Vue template syntax. Vue 3 Composition API reference.
metadata:
  author: AlexanderOpran
---

# Vue 3 Skill

> Based on Vue 3.5. Always use Composition API with `<script setup lang="ts">`.

## Preferences
- Prefer TypeScript over JavaScript
- Prefer `<script setup lang="ts">` over `<script>`
- For performance, prefer `shallowRef` over `ref` if deep reactivity is not needed
- Always use Composition API over Options API
- Discourage using Reactive Props Destructure

## Component Template

```vue
<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'

const props = defineProps<{
  title: string
  count?: number
}>()

const emit = defineEmits<{
  update: [value: string]
}>()

const model = defineModel<string>()

const doubled = computed(() => (props.count ?? 0) * 2)

watch(() => props.title, (newVal) => {
  console.log('Title changed:', newVal)
})
</script>

<template>
  <div>{{ title }} - {{ doubled }}</div>
</template>
```

## Key Imports

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
> Source: [AlexanderOpran/nuxt-build-issue](https://github.com/AlexanderOpran/nuxt-build-issue) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
