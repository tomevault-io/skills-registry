---
name: vue-dev
description: Vue.js 3 frontend development Use when this capability is needed.
metadata:
  author: macedot
---

# Vue.js 3 Development Guide

## Project Structure
```
frontend/
├── src/
│   ├── components/   # Reusable Vue components
│   ├── views/        # Page-level components
│   ├── stores/       # Pinia state stores
│   ├── router.ts     # Vue Router configuration
│   └── main.ts       # Application entry point
├── package.json
└── vite.config.ts
```

## Component Conventions

### File Naming
- Use PascalCase: `VideoInfo.vue`, `DownloadProgress.vue`
- One component per file

### Script Section
```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const props = defineProps<{
  title: string
}>()

const emit = defineEmits<{
  (e: 'update', value: string): void
}>()
</script>
```

### Template Section
```vue
<template>
  <div class="component">
    <h1>{{ title }}</h1>
  </div>
</template>
```

### Style Section
```vue
<style scoped>
.component {
  padding: 1rem;
}
</style>
```

## Pinia Stores

```typescript
// stores/downloads.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useDownloadStore = defineStore('downloads', () => {
  const items = ref<Download[]>([])
  
  const active = computed(() =>
    items.value.filter(d => d.status === 'downloading')
  )
  
  return { items, active }
})
```

## Key Commands
- `npm run dev` - Start dev server
- `npm run build` - Production build
- `npm run lint` - Run ESLint
- `npm run check` - Run all checks

---
> Source: [macedot/dldl](https://github.com/macedot/dldl) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
