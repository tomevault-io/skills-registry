---
name: vue-coding-standards
description: | Use when this capability is needed.
metadata:
  author: generative-bricks
---

# Vue Coding Standards & Best Practices

Vue 3 coding standards for the oakiv-website project. Based on official Vue style guide and current best practices (Vue 3.5+, Pinia, Vue Router 4).

## Code Quality Principles

1. **Readability First** - Clear names, self-documenting code, consistent formatting
2. **KISS** - Simplest solution that works, no over-engineering
3. **DRY** - Extract common logic into composables, create reusable components
4. **YAGNI** - Don't build features before needed

---

## Vue 3 Component Standards

### Use Composition API with `<script setup>`

```vue
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

interface Props {
  title: string
  count?: number
}

const props = withDefaults(defineProps<Props>(), {
  count: 0
})

const emit = defineEmits<{
  update: [value: number]
  close: []
}>()

const localCount = ref(props.count)
const doubleCount = computed(() => localCount.value * 2)

function increment() {
  localCount.value++
  emit('update', localCount.value)
}
</script>

<template>
  <div class="counter">
    <h2>{{ title }}</h2>
    <button @click="increment">Count: {{ localCount }}</button>
  </div>
</template>
```

### Component Naming

| Type | Pattern | Example |
|------|---------|---------|
| Base components | `Base` prefix | `BaseButton.vue`, `BaseInput.vue` |
| Layout | `Layout` prefix | `LayoutHeader.vue`, `LayoutSidebar.vue` |
| Feature | Feature prefix | `UserProfile.vue`, `UserSettings.vue` |
| Related | Shared prefix | `SearchInput.vue`, `SearchResults.vue` |

**Rules:**
- PascalCase for files: `UserProfile.vue`
- Multi-word names required (avoid `Button.vue`)
- Related components share prefix

### Props & Emits

```typescript
// Props - always typed with defaults
interface Props {
  userId: string
  isActive?: boolean
  items?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  isActive: false,
  items: () => []
})

// Emits - always typed
const emit = defineEmits<{
  'update:modelValue': [value: string]
  submit: [data: FormData]
}>()
```

**Prop Casing:** camelCase in script, both camelCase/kebab-case work in templates. Pick one, be consistent.

### defineModel (Vue 3.4+)

```typescript
// Simplifies v-model - no manual emit needed
const modelValue = defineModel<string>()
const count = defineModel<number>('count', { default: 0 })
```

### useTemplateRef (Vue 3.5+)

```typescript
import { useTemplateRef, onMounted } from 'vue'

const inputEl = useTemplateRef<HTMLInputElement>('input')

onMounted(() => inputEl.value?.focus())
// Template: <input ref="input" />
```

---

## Pinia State Management

Use **Setup Syntax** (recommended over Options):

```typescript
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', () => {
  // State
  const user = ref<User | null>(null)
  const isLoggedIn = ref(false)

  // Getters
  const fullName = computed(() =>
    user.value ? `${user.value.firstName} ${user.value.lastName}` : 'Guest'
  )

  // Actions
  async function login(email: string, password: string) {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      body: JSON.stringify({ email, password })
    })
    user.value = (await response.json()).user
    isLoggedIn.value = true
  }

  return { user, isLoggedIn, fullName, login }
})
```

**Usage in components:**

```typescript
import { storeToRefs } from 'pinia'
const userStore = useUserStore()

// Use storeToRefs for reactive destructuring
const { user, fullName } = storeToRefs(userStore)

// Actions can be destructured directly
const { login, logout } = userStore
```

---

## Vue Router

### Lazy Loading Routes

```typescript
const Home = () => import('@/views/Home.vue')
const UserProfile = () => import('@/views/UserProfile.vue')

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', name: 'home', component: Home },
    { path: '/user/:id', component: UserProfile, props: true, meta: { requiresAuth: true } }
  ]
})
```

### Navigation Guards

```typescript
router.beforeEach((to, from) => {
  const userStore = useUserStore()
  if (to.meta.requiresAuth && !userStore.isLoggedIn) {
    return { path: '/login', query: { redirect: to.fullPath } }
  }
  return true
})
```

### In-Component Guards

```typescript
import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router'

onBeforeRouteLeave(() => {
  if (hasUnsavedChanges.value) {
    return window.confirm('Discard unsaved changes?')
  }
})
```

---

## Template Best Practices

### Conditional Rendering

```vue
<div v-if="loading">Loading...</div>
<div v-else-if="error">{{ error }}</div>
<div v-else-if="data"><UserCard :user="data" /></div>
<div v-else>No data</div>
```

### List Rendering

```vue
<!-- Always use unique key -->
<li v-for="item in items" :key="item.id">{{ item.name }}</li>

<!-- Never use index as key -->
<li v-for="(item, index) in items" :key="index"><!-- BAD --></li>
```

---

## Performance

| Pattern | Use Case |
|---------|----------|
| `computed` | Derived state (cached) |
| `v-once` | Static content, render once |
| `v-memo` | Skip re-render when deps unchanged |
| `defineAsyncComponent` | Lazy load heavy components |
| `shallowRef` | When deep reactivity not needed |

```typescript
// Lazy load heavy components
const HeavyChart = defineAsyncComponent(() =>
  import('@/components/HeavyChart.vue')
)
```

---

## File Organization

```
src/
├── components/
│   ├── Base/           # BaseButton, BaseInput
│   ├── Layout/         # LayoutHeader, LayoutFooter
│   └── [Feature]/      # Feature-specific
├── composables/        # useAuth, useDebounce
├── stores/             # Pinia stores
├── router/             # index.ts, guards.ts
├── types/              # TypeScript interfaces
├── views/              # Page components
└── utils/              # Helper functions
```

---

## Project-Specific Patterns (oakiv-website)

### Tailwind CSS 4
- Use Tailwind utility classes exclusively (no inline styles)
- Brand colors: `oak-green-primary`, `oak-gold`, `oak-cream`
- Use `@apply` sparingly in `<style scoped>`

### Amplify Integration
- Types in `src/types/index.ts` mirror Amplify Data schema
- Stores handle mock data until backend deployed
- Chat uses Bedrock Lambda via Function URL

### Form Handling
- Form state managed in parent view components with reactive refs
- Use native HTML form elements with v-model (no custom form components yet)
- Validation handled in submit handlers

---

## Additional Resources

### Reference Files

For detailed patterns and examples, consult:

- **`references/composables.md`** - Composable patterns (useDebounce, useAsync, useUser)
- **`references/pinia-patterns.md`** - Advanced Pinia store patterns and composition
- **`references/vue-router-patterns.md`** - Route organization and guard patterns
- **`references/testing.md`** - Vue Test Utils and component testing

### Quick Reference

| Task | Pattern |
|------|---------|
| Create component | `<script setup lang="ts">` + typed props/emits |
| Two-way binding | `defineModel<T>()` (Vue 3.4+) |
| Template ref | `useTemplateRef<T>('name')` (Vue 3.5+) |
| State management | Pinia setup store syntax |
| Route lazy load | `() => import('@/views/X.vue')` |
| Derived state | Always use `computed()` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/generative-bricks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
