---
name: vue-patterns
description: > Use when this capability is needed.
metadata:
  author: hjemmesidekongen
---

# Vue Patterns

## Composable Design Patterns

**When to extract**: logic uses 2+ reactive primitives together and appears in multiple components. Single `ref()` doesn't warrant a composable.

**Return shape**: always return an object (`{ data, loading, error, refresh }`), never a single ref. Allows destructuring and future extension without breaking callers.

**Naming**: `use` prefix, noun-oriented (`useAuth`, `useFormValidation`). Name describes the concern, not the implementation. Composables can call other composables — keep chains shallow (max 3 levels).

**Cleanup**: if a composable sets up listeners/timers/subscriptions, use `onUnmounted` internally. Callers shouldn't need to remember cleanup.

## provide/inject for Dependency Injection

Use for cross-cutting concerns needing 3+ levels of prop drilling: theme, locale, feature flags, form context. **Testing benefit**: `provide` in test wrappers replaces real services with mocks. Use `InjectionKey<T>` for type safety.

**Scope control**: provide at the nearest common ancestor, not app root — root-level provide is effectively a global (use Pinia instead). Always provide a fallback in `inject('key', defaultValue)` or throw explicitly.

## Vue Compiler Macros

**`defineModel()`**: two-way binding without manual emit boilerplate. `const model = defineModel<string>()` replaces the `modelValue` prop + `update:modelValue` emit pair. Supports named models: `defineModel('title')`.

**`defineSlots()`**: type-safe slot definitions. Enforces slot prop types at compile time. Use in library components where consumers need slot prop documentation.

**`defineExpose()`**: explicitly declare what `ref` on the component can access. Default in `<script setup>` is nothing exposed. Only expose imperative methods (focus, reset, validate) — never expose internal state.

## SSR/Nuxt Hydration Gotchas

**Hydration mismatch**: server and client must render identical initial HTML. Common causes: `Date.now()`, `Math.random()`, browser-only APIs (`window`, `localStorage`), conditional rendering based on screen size.

**Fix pattern**: `<ClientOnly>` wrapper for browser-dependent content. For data: use `useState()` (Nuxt) which serializes server state to `__NUXT_DATA__` for client hydration.

**Serialization limits**: `provide`/`inject` values aren't serialized across SSR boundary. Pinia state is (via `useNuxtApp().payload`). Plan store shape around what's JSON-serializable.

**`onMounted` is client-only**: any DOM measurement, intersection observer, or animation setup must go in `onMounted`. Code at `<script setup>` top-level runs on both server and client.

## Pinia Plugin Patterns and Store Composition

**Plugins**: `pinia.use(({ store }) => { store.$subscribe(...) })`. Common uses: persistence (`pinia-plugin-persistedstate`), undo/redo (track mutation history), logging.

**Store composition**: stores can import and use other stores. `useCartStore` calls `useProductStore().getProduct(id)`. Avoid circular dependencies — extract shared logic to a composable instead.
See `references/process.md` for reactivity pitfalls, slots, Teleport, Suspense, and anti-patterns.

---
> Source: [hjemmesidekongen/ai](https://github.com/hjemmesidekongen/ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-25 -->
