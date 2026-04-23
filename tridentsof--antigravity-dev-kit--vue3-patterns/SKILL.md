---
name: vue3-patterns
description: description: Vue3 Composition API, Pinia, Vue Router, and Tailwind CSS patterns Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: vue3-patterns
description: Vue3 Composition API, Pinia, Vue Router, and Tailwind CSS patterns
---

# Vue3 Patterns

> Modern Vue3 with Composition API, TypeScript, Pinia, and **Tailwind CSS**.

---

## Component Structure

```vue
<script setup lang="ts">
// 1. Imports
import { ref, computed, onMounted } from 'vue'

// 2. Props & Emits
const props = defineProps<{ title: string }>()
const emit = defineEmits<{ (e: 'update', value: string): void }>()

// 3. Reactive State
const count = ref(0)

// 4. Computed
const doubled = computed(() => count.value * 2)

// 5. Methods
const increment = () => count.value++

// 6. Lifecycle
onMounted(() => console.log('Mounted'))
</script>

<template>
  <div class="p-4 rounded-lg bg-white shadow-md">
    <h2 class="text-xl font-semibold">{{ title }}</h2>
    <p class="mt-2 text-gray-600">Count: {{ count }}</p>
  </div>
</template>
```

---

## Tailwind CSS with Vue3

### Styling Approach

| Approach | When to Use |
|----------|-------------|
| **Utility classes** | Default - inline in template |
| **@apply in scoped** | Complex repeated patterns |
| **CSS variables** | Dynamic theming |

### Button with Tailwind

```vue
<template>
  <button class="
    px-4 py-2 font-medium rounded-lg
    bg-primary text-white
    transition-all duration-200 ease-out
    hover:-translate-y-0.5 hover:shadow-lg
    active:scale-95
    focus:outline-none focus:ring-2 focus:ring-primary/50
    disabled:opacity-50 disabled:cursor-not-allowed
  ">
    <slot />
  </button>
</template>
```

### Dynamic Classes

```vue
<template>
  <div :class="[
    'p-4 rounded-lg transition-colors',
    isActive ? 'bg-primary text-white' : 'bg-gray-100',
    { 'opacity-50': disabled }
  ]">
    Content
  </div>
</template>
```

---

## Vue Transitions with Tailwind

```vue
<template>
  <Transition
    enter-active-class="transition-all duration-300 ease-out"
    enter-from-class="opacity-0 translate-y-4"
    enter-to-class="opacity-100 translate-y-0"
    leave-active-class="transition-all duration-200 ease-in"
    leave-from-class="opacity-100"
    leave-to-class="opacity-0 translate-y-4"
  >
    <div v-if="show" class="p-4 bg-white rounded-lg shadow">
      Animated content
    </div>
  </Transition>
</template>
```

---

## State Management

| Scope | Solution |
|-------|----------|
| Component | `ref()`, `reactive()` |
| Parent-child | Props + Emits |
| Global | Pinia store |

### Pinia Store

```typescript
export const useUserStore = defineStore('user', () => {
  const user = ref<User | null>(null)
  const isLoggedIn = computed(() => !!user.value)
  
  async function login(credentials: Credentials) {
    user.value = await api.login(credentials)
  }
  
  return { user, isLoggedIn, login }
})
```

---

## Architecture Layers

| Layer | Responsibility | Examples |
|-------|---------------|----------|
| **View** | Presentation only | LoginView.vue, StudentDashboard.vue |
| **Composable** | Business logic | useLogin, useAssignmentSubmission |
| **Service** | API calls | authService, assignmentService |
| **API** | HTTP requests | api/auth.js, api/assignment.js |

### Proper Flow

```
View → Composable → Service → API
```

### ❌ DON'T: Business Logic in Views

```vue
<!-- BAD: LoginView.vue -->
<script setup>
async function handleLogin(formData) {
  loading.value = true
  try {
    const data = await loginApi(formData.email, formData.password)
    const decoded = jwtDecode(data.token)
    // ... 60+ lines of logic
  } catch (e) {
    // error handling
  }
}
</script>
```

### ✅ DO: Extract to Composable

```vue
<!-- GOOD: LoginView.vue -->
<script setup>
import { useLogin } from '@/composables/auth/useLogin'

const { login, isLoading } = useLogin()
</script>
```

```javascript
// composables/auth/useLogin.js
export function useLogin() {
  const isLoading = ref(false)
  
  const login = async (email, password) => {
    isLoading.value = true
    try {
      const data = await authService.login(email, password)
      // Handle success
    } catch (error) {
      // Handle error
    } finally {
      isLoading.value = false
    }
  }
  
  return { login, isLoading }
}
```

---

## Composables

```typescript
// composables/useCounter.ts
export function useCounter(initial = 0) {
  const count = ref(initial)
  const increment = () => count.value++
  const decrement = () => count.value--
  return { count, increment, decrement }
}
```

---

## Tailwind Config

```javascript
// tailwind.config.js
module.exports = {
  content: [
    './index.html',
    './src/**/*.{vue,js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        primary: '#0d9488',
        accent: '#f59e0b',
      },
      fontFamily: {
        sans: ['DM Sans', 'sans-serif'],
        display: ['Space Grotesk', 'sans-serif'],
      }
    }
  }
}
```

---

## TypeScript

| Pattern | Example |
|---------|---------|
| Props | `defineProps<{ id: number }>()` |
| Emits | `defineEmits<{ (e: 'click'): void }>()` |
| Ref typing | `const user = ref<User \| null>(null)` |

---

## DO / DON'T

| Do | Don't |
|-------|---------|
| Composition API | Options API |
| TypeScript strict | `any` type |
| Pinia for global state | Vuex |
| Tailwind utilities | Inline styles |
| Custom Tailwind config | Default colors only |
| Small components | Giant components |
| **Composables for logic** | **Business logic in views** |
| **View → Composable → Service** | **View → Service directly** |
| **Reuse existing components** | **Duplicate components** |


---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
