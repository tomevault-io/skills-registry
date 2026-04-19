---
name: vuejs-migration-expert
description: Complete Vue.js 3 expertise for migrating React applications to Vue.js + Vite stack. Combines Vue 3 Composition API patterns, component architecture, Pinia state management, and migration-specific best practices. Use when implementing Vue.js features during migration from React. Use when this capability is needed.
metadata:
  author: kukretipushpendra
---

# Vue.js Migration Expert

Complete Vue.js 3 expertise for migrating React applications to modern Vue.js + Vite stack. This skill combines deep Vue.js knowledge with practical Vite tooling and React-to-Vue migration patterns.

## Role Definition

You are a senior Vue.js engineer specializing in React-to-Vue migrations. You understand Vue 3 Composition API, TypeScript, Pinia state management, and the challenges of maintaining 100% feature parity when converting React code to Vue.js.

## When to Use This Skill

- Implementing Vue.js components during migration from React
- Converting React patterns to Vue.js equivalents
- Making architecture decisions (state management, folder structure)
- Optimizing Vue.js performance
- Setting up project foundation with proper tooling
- Implementing forms with validation
- Writing type-safe Vue.js code with TypeScript

## Core Workflow

1. **Analyze React source** - Read legacy React components and understand patterns
2. **Map to Vue patterns** - Convert React hooks/state to Vue Composition API
3. **Implement** - Write TypeScript Vue components with proper types
4. **Lint & Type-check** - Run `npm run lint` and `npm run type-check` before commit
5. **Verify parity** - Ensure 100% visual and functional parity with legacy React

## React → Vue.js Pattern Mappings

### State Management

| React | Vue 3 |
|-------|-------|
| `useState(initial)` | `const state = ref(initial)` |
| `useState<Type>()` | `const state = ref<Type>()` |
| `setCount(count + 1)` | `count.value++` |
| `setState({ ...state, key: value })` | `Object.assign(state, { key: value })` with `reactive()` |

### Effects & Lifecycle

| React | Vue 3 |
|-------|-------|
| `useEffect(() => {}, [])` | `onMounted(() => {})` |
| `useEffect(() => {}, [dep])` | `watch(dep, () => {})` |
| `useEffect(() => () => cleanup)` | `onUnmounted(() => cleanup)` |
| `useLayoutEffect` | `onMounted` (runs after DOM update) |

### Computed & Memoization

| React | Vue 3 |
|-------|-------|
| `useMemo(() => compute, [deps])` | `const result = computed(() => compute)` |
| `useCallback(fn, [deps])` | Just use regular functions (Vue auto-optimizes) |

### Refs

| React | Vue 3 |
|-------|-------|
| `const ref = useRef(null)` | `const refEl = ref<HTMLElement \| null>(null)` |
| `ref.current` | `refEl.value` |
| `<div ref={ref}>` | `<div ref="refEl">` |

### Context & Props

| React | Vue 3 |
|-------|-------|
| `createContext` + `useContext` | `provide()` + `inject()` |
| Props with interface | `defineProps<Interface>()` |
| `props.children` | `<slot />` |
| Named children via props | `<slot name="header" />` |

### Events

| React | Vue 3 |
|-------|-------|
| `onClick={handler}` | `@click="handler"` |
| `onChange={(e) => setValue(e.target.value)}` | `@input="handler"` or `v-model` |
| `onSubmit={(e) => { e.preventDefault(); }}` | `@submit.prevent="handler"` |
| Props callback pattern | `defineEmits(['event'])` + `emit('event', data)` |

### Conditional Rendering

| React | Vue 3 |
|-------|-------|
| `{condition && <div>}` | `<div v-if="condition">` |
| `{condition ? <A /> : <B />}` | `<A v-if="condition" /> <B v-else />` |
| `{items.map(item => <Item key={item.id} />)}` | `<Item v-for="item in items" :key="item.id" />` |

### Custom Hooks → Composables

```typescript
// React Hook
function useCounter(initial: number) {
  const [count, setCount] = useState(initial);
  const increment = () => setCount(c => c + 1);
  return { count, increment };
}

// Vue Composable
function useCounter(initial: number) {
  const count = ref(initial);
  const increment = () => count.value++;
  return { count, increment };
}
```

## Vue Component Template

```vue
<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue';
import type { PropType } from 'vue';

// Props (like React interface Props)
interface Props {
  title: string;
  items?: string[];
}
const props = withDefaults(defineProps<Props>(), {
  items: () => [],
});

// Emits (like React callback props)
const emit = defineEmits<{
  (e: 'update', value: string): void;
  (e: 'close'): void;
}>();

// State (like useState)
const count = ref(0);
const inputValue = ref('');

// Computed (like useMemo)
const doubleCount = computed(() => count.value * 2);

// Methods
const increment = () => {
  count.value++;
  emit('update', String(count.value));
};

// Lifecycle (like useEffect with [])
onMounted(() => {
  console.log('Component mounted');
});

// Watcher (like useEffect with dependencies)
watch(count, (newVal, oldVal) => {
  console.log(`Count changed from ${oldVal} to ${newVal}`);
});
</script>

<template>
  <div class="container">
    <h1>{{ props.title }}</h1>
    <p>Count: {{ count }} (Double: {{ doubleCount }})</p>
    <button @click="increment">Increment</button>

    <ul v-if="props.items.length > 0">
      <li v-for="item in props.items" :key="item">{{ item }}</li>
    </ul>
    <p v-else>No items</p>

    <slot />
  </div>
</template>

<style scoped>
.container {
  padding: 1rem;
}
</style>
```

## Pinia Store (Zustand Equivalent)

```typescript
// React Zustand
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));

// Vue Pinia
import { defineStore } from 'pinia';

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
  }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++;
    },
  },
});

// Usage in component
const store = useCounterStore();
store.increment();
console.log(store.count);
```

## Form Handling (React Hook Form → VeeValidate)

```vue
<script setup lang="ts">
import { useForm, useField } from 'vee-validate';
import { toTypedSchema } from '@vee-validate/zod';
import { z } from 'zod';

const schema = toTypedSchema(
  z.object({
    email: z.string().email('Invalid email address'),
    password: z.string().min(8, 'Password must be at least 8 characters'),
  })
);

const { handleSubmit, errors } = useForm({
  validationSchema: schema,
});

const { value: email } = useField('email');
const { value: password } = useField('password');

const onSubmit = handleSubmit((values) => {
  console.log('Form submitted:', values);
});
</script>

<template>
  <form @submit="onSubmit">
    <div>
      <input v-model="email" type="email" placeholder="Email" />
      <span v-if="errors.email" class="error">{{ errors.email }}</span>
    </div>

    <div>
      <input v-model="password" type="password" placeholder="Password" />
      <span v-if="errors.password" class="error">{{ errors.password }}</span>
    </div>

    <button type="submit">Submit</button>
  </form>
</template>
```

## API Service Pattern

```typescript
// src/services/api/axios-client.ts
import axios from 'axios';

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 15000,
});

apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default apiClient;
```

## Composable for Data Fetching

```typescript
// src/composables/useAsync.ts
import { ref, Ref } from 'vue';

interface UseAsyncReturn<T> {
  data: Ref<T | null>;
  loading: Ref<boolean>;
  error: Ref<string | null>;
  execute: () => Promise<void>;
}

export function useAsync<T>(
  asyncFn: () => Promise<T>,
  immediate = true
): UseAsyncReturn<T> {
  const data = ref<T | null>(null) as Ref<T | null>;
  const loading = ref(false);
  const error = ref<string | null>(null);

  const execute = async () => {
    loading.value = true;
    error.value = null;
    try {
      data.value = await asyncFn();
    } catch (e) {
      error.value = e instanceof Error ? e.message : 'An error occurred';
    } finally {
      loading.value = false;
    }
  };

  if (immediate) {
    execute();
  }

  return { data, loading, error, execute };
}
```

## Project Foundation Setup

### Required Dependencies
```bash
npm install vue-router@4 pinia axios
npm install vee-validate @vee-validate/zod zod
npm install -D @vitejs/plugin-vue typescript vue-tsc
npm install -D eslint prettier
npm install -D @typescript-eslint/eslint-plugin @typescript-eslint/parser
npm install -D eslint-plugin-vue
```

### package.json Scripts
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext .vue,.ts,.tsx --report-unused-disable-directives --max-warnings 0",
    "lint:fix": "eslint . --ext .vue,.ts,.tsx --fix",
    "format": "prettier --write \"src/**/*.{vue,ts,tsx,json,css,scss,md}\"",
    "format:check": "prettier --check \"src/**/*.{vue,ts,tsx,json,css,scss,md}\"",
    "type-check": "vue-tsc --noEmit"
  }
}
```

### ESLint Configuration (.eslintrc.cjs)
```javascript
module.exports = {
  root: true,
  env: { browser: true, es2020: true, node: true },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:vue/vue3-recommended',
  ],
  parser: 'vue-eslint-parser',
  parserOptions: {
    parser: '@typescript-eslint/parser',
    ecmaVersion: 2020,
    sourceType: 'module',
  },
  plugins: ['@typescript-eslint'],
  rules: {
    'vue/multi-word-component-names': 'off',
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
  },
};
```

## Migration-Specific Constraints

### MUST DO
- Use TypeScript with strict mode
- Run `npm run type-check` before every commit
- Run `npm run lint` before every commit
- Maintain 100% visual and functional parity with legacy React
- Copy CSS exactly as-is (only update resource paths)
- Use same validation messages as legacy (exact text)
- Keep same field order, button labels, layout
- Use Vue 3 Composition API (`<script setup>`)
- Clean up watchers and event listeners

### MUST NOT DO
- Mutate reactive state directly (use `.value` for refs)
- Use Options API (use Composition API only)
- Modify CSS styles or selectors (only update paths)
- Skip type-checking or linting before commit
- Change validation messages from legacy
- Use v-model with props (use emit pattern)

## Knowledge Reference

Vue.js 3, Composition API, TypeScript, Pinia, VeeValidate, Zod, Vue Router 4, Vite, ESLint, Prettier, Axios, Composables, provide/inject, Teleport, Suspense

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kukretipushpendra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
