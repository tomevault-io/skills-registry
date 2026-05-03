---
name: vue-skilld
description: ALWAYS use when editing or working with *.vue files or code importing "vue". Consult for debugging, best practices, or modifying vue, core. Use when this capability is needed.
metadata:
  author: synmux
---

# vuejs/core `vue@3.5.32`

**Tags:** csp: 1.0.28-csp, v2-latest: 2.7.16, legacy: 2.7.16

**References:** [package.json](./.skilld/pkg/package.json) • [README](./.skilld/pkg/README.md) • [Docs](./.skilld/docs/_INDEX.md) • [Issues](./.skilld/issues/_INDEX.md) • [Discussions](./.skilld/discussions/_INDEX.md) • [Releases](./.skilld/releases/_INDEX.md)

## Search

Use `skilld search "query" -p vue` instead of grepping `.skilld/` directories. Run `skilld search --guide -p vue` for full syntax, filters, and operators.

<!-- skilld:api-changes -->

## API Changes

This section documents version-specific API changes in Vue 3.x — prioritising recent minor releases (3.3, 3.4, 3.5).

- BREAKING: `@vnodeXXX` event listeners — removed in v3.4, now a compiler error. Use `@vue:XXX` listeners instead (e.g. `@vue:mounted` replaces `@vnode-mounted`) [source](./.skilld/releases/blog-3.4.md#other-removed-features)

- BREAKING: Reactivity Transform (`$ref`, `$computed`, `$toRef`) — deprecated in v3.3, removed in v3.4. Code using `$ref()` will fail silently (treated as a regular function call). Use the Vue Macros plugin to retain the feature [source](./.skilld/releases/blog-3.4.md#other-removed-features)

- BREAKING: `v-is` directive — removed in v3.4. Use the `is` attribute with `vue:` prefix instead: `<component :is="vue:my-component">` [source](./.skilld/releases/blog-3.4.md#other-removed-features)

- BREAKING: Global `JSX` namespace — Vue no longer registers it by default since v3.4. Set `jsxImportSource` to `'vue'` in `tsconfig.json` for TSX usage, or reference `vue/jsx` to restore global behaviour [source](./.skilld/releases/blog-3.4.md#global-jsx-namespace)

- BREAKING: `app.config.unwrapInjectedRef` — removed in v3.4. Was deprecated in v3.3 (enabled by default). Injected refs are now always unwrapped; the option no longer exists [source](./.skilld/releases/blog-3.4.md#other-removed-features)

- BREAKING: `watch()` computed sources — since v3.4, `watchEffect` callbacks only fire when the computed result actually changes, not on every dependency mutation. Code relying on the callback firing for same-value recomputations will silently behave differently [source](./.skilld/releases/blog-3.4.md#more-efficient-reactivity-system)

- NEW: `useTemplateRef()` — new in v3.5, replaces the pattern of using `ref()` with a variable name matching a static `ref` attribute. Supports dynamic ref bindings via runtime string IDs. Returns a `Readonly<ShallowRef<T | null>>` [source](./.skilld/releases/blog-3.5.md#usetemplateref)

- NEW: `useId()` — new in v3.5, generates unique-per-application IDs stable across server and client renders. Use with `app.config.idPrefix` to avoid collisions between multiple app instances [source](./.skilld/releases/blog-3.5.md#useid)

- NEW: `onWatcherCleanup()` — new in v3.5, globally imported API for registering cleanup callbacks in watchers (e.g. aborting stale fetch requests). Alternative to the `onCleanup` parameter in the watcher callback [source](./.skilld/releases/blog-3.5.md#onwatchercleanup)

- NEW: `getCurrentWatcher()` — new in v3.5, returns the currently active watcher instance. Useful for library authors building reactive utilities [source](./.skilld/releases/CHANGELOG.md:L625)

- NEW: `hydrateOnVisible()`, `hydrateOnIdle()`, `hydrateOnInteraction()`, `hydrateOnMediaQuery()` — new lazy hydration strategies in v3.5 for async components via `defineAsyncComponent({ hydrate: hydrateOnVisible() })` [source](./.skilld/releases/blog-3.5.md#lazy-hydration)

- NEW: `<Teleport defer>` — new `defer` prop in v3.5 allows teleporting to target elements rendered later in the same render cycle. Without `defer`, the target must exist before the Teleport mounts [source](./.skilld/releases/blog-3.5.md#deferred-teleport)

- NEW: Reactive props destructure — stabilised in v3.5, enabled by default. Destructured `defineProps` variables are reactive. Use native JS default values instead of `withDefaults()`: `const { count = 0 } = defineProps<{ count?: number }>()`. Must wrap in getter for `watch()` [source](./.skilld/releases/blog-3.5.md#reactive-props-destructure)

- NEW: `watch()` `deep` option accepts a number — new in v3.5, pass a number to control watch depth: `watch(source, cb, { deep: 2 })` for partial deep watching [source](./.skilld/releases/CHANGELOG.md:L627)

- NEW: `WatchHandle` with `pause()`/`resume()` — new in v3.5, the return value of `watch()`/`watchEffect()` now supports `.pause()` and `.resume()` methods to temporarily suspend and restart the watcher [source](./.skilld/releases/CHANGELOG.md:L626)

- NEW: `defineModel()` — stabilised in v3.4. Simplifies `v-model` on components: returns a directly-mutable ref instead of manual prop+emit. Supports `v-model` modifiers. Was experimental in v3.3 [source](./.skilld/releases/blog-3.4.md#definemodel-is-now-stable)

- NEW: `v-bind` same-name shorthand — new in v3.4. `:id` is shorthand for `:id="id"`. Applies to all `v-bind` directives where the attribute name matches the bound variable [source](./.skilld/releases/blog-3.4.md#v-bind-same-name-shorthand)

- NEW: `defineOptions()` — new in v3.3. Declares component options (e.g. `inheritAttrs: false`) directly in `<script setup>` without a separate `<script>` block [source](./.skilld/releases/blog-3.3.md#defineoptions)

- NEW: `defineSlots()` — new in v3.3. Type-only macro for declaring expected slots and their prop types in `<script setup>`. Returns the same slots object as `useSlots()` [source](./.skilld/releases/blog-3.3.md#typed-slots-with-defineslots)

- NEW: `toValue()` — new in v3.3. Normalises values/getters/refs into plain values. Preferred over `unref()` in composables because it also handles getter functions: `toValue(() => props.foo)` [source](./.skilld/releases/blog-3.3.md#better-getter-support-with-toref-and-tovalue)

- NEW: `app.onUnmount()` — new in v3.5, registers cleanup functions at the app level that run when the app is unmounted [source](./.skilld/releases/CHANGELOG.md:L659)

**Also changed:** `useHost()` custom element helper v3.5 · `useShadowRoot()` custom element helper v3.5 · `app.config.throwUnhandledErrorInProduction` v3.5 · `TemplateRef` type export v3.5.14 · `MultiWatchSources` type export v3.5 · computed getter/setter types can be unrelated v3.5 · `defineEmits` named tuple syntax v3.3 · `onEffectCleanup()` in `@vue/reactivity` v3.5 · `onScopeDispose` `failSilently` arg v3.5 · `data-allow-mismatch` attribute v3.5 · `defineCustomElement` `shadowRoot: false` option v3.5 · `defineCustomElement` `configureApp` option v3.5 · `Teleport` directly inside `Transition` v3.5

<!-- /skilld:api-changes -->

<!-- skilld:best-practices -->

## Plan: Generate Vue 3.5.32 SKILL.md Best Practices

## Context

Generating `_BEST_PRACTICES.md` for the `vue-skilld` package (Vue v3.5.32). This file will contain 14 non-obvious best practice items extracted from the local `.skilld/` reference material, covering patterns an LLM wouldn't already know from general training data.

## Research Summary

I've read the following key files:

- **Docs index** — full doc tree structure
- **Performance guide** — props stability, v-memo, computed stability (3.4+), shallowRef for large structures, list virtualisation
- **Composables guide** — `toValue()` for normalising inputs, return refs not reactive objects, cleanup in `onUnmounted`, SSR-safe side effects, sync-only composable calls
- **Reactivity in depth** — shallowRef for external state, Immer integration, computed/watcher debugging hooks
- **TypeScript + Composition API** — `InjectionKey`, `useTemplateRef` auto-inference (3.5+), `ComponentExposed` for generics, `GlobalDirectives` augmentation
- **Watchers guide** — `onWatcherCleanup()` (3.5+), `deep` as a number (3.5+), `once: true` (3.4+), `watchEffect` only tracks before first `await`, `flush: 'post'`/`'sync'` options
- **script setup** — `defineModel()` with `get`/`set` transformers, `defineOptions()`, `defineSlots()`, generics via `generic` attribute, `@vue-generic` directive, `vNameOfDirective` convention
- **Compile-time flags** — `__VUE_OPTIONS_API__`, `__VUE_PROD_HYDRATION_MISMATCH_DETAILS__`
- **v-model guide** — `defineModel()` desync warning with default values, modifier/transformer pattern
- **Provide/Inject** — `readonly()` wrapper, mutator function pattern, Symbol keys with `InjectionKey`
- **KeepAlive** — `onActivated`/`onDeactivated`, `max` prop as LRU cache
- **Computed** — previous value param (3.4+), computed stability optimisation
- **Async components** — lazy hydration strategies (3.5+): `hydrateOnIdle`, `hydrateOnVisible`, `hydrateOnInteraction`, `hydrateOnMediaQuery`
- **Vue 3.5 blog** — reactive props destructure, `useTemplateRef()`, deferred `<Teleport defer>`, `useId()`, `onWatcherCleanup()`
- **Changelog** — recent fixes (3.5.25+): `provide` warn after mount, `customRef` different getter/setter types

## API Verification

All referenced APIs verified as real exports in `@vue/runtime-core` and `@vue/reactivity` `.d.ts` files:

- `useTemplateRef`, `useId`, `onWatcherCleanup`, `hydrateOnIdle`, `hydrateOnVisible`
- `toValue`, `shallowRef`, `shallowReactive`, `InjectionKey`
- `watchPostEffect`, `watchSyncEffect`

## 14 Best Practice Items (draft outline)

1. **Reactive Props Destructure** with native defaults (3.5+) — replaces `withDefaults`
2. **`toValue()` in composables** — normalise ref/getter/raw inputs
3. **Composable return convention** — return plain objects of refs, not reactive
4. **`onWatcherCleanup()`** for aborting stale async work (3.5+)
5. **Computed previous value parameter** (3.4+) — avoid unnecessary re-triggers for object computeds
6. **Props stability** — compute derived booleans in parent to avoid cascading child updates
7. **`shallowRef()` for large immutable structures** — opt out of deep reactivity
8. **`useTemplateRef()`** over plain ref naming convention (3.5+)
9. **Provide/inject with `InjectionKey<T>`** + `readonly()` for one-way data flow
10. **`defineModel()` with `get`/`set` transformers** for modifier handling (3.4+)
11. **Compile-time flag `__VUE_OPTIONS_API__: false`** to tree-shake Options API
12. **Lazy hydration strategies** for SSR async components (3.5+)
13. **`deep` option as a number** for depth-limited watching (3.5+)
14. **`useId()`** for SSR-stable unique IDs (3.5+)

## Diversity Check

Coverage areas:

- Reactivity (items 2, 5, 6, 7) — 4 items (29%)
- Component API / Props (items 1, 10) — 2 items (14%)
- Composition patterns (items 3, 4, 8) — 3 items (21%)
- TypeScript / DI (item 9) — 1 item (7%)
- Build / Compile (item 11) — 1 item (7%)
- SSR (items 12, 14) — 2 items (14%)
- Watchers (item 13) — 1 item (7%)

No single area exceeds 40%. At least 7 distinct areas covered.

## Output

Write to `/Users/dave/src/github.com/synmux/syn-horse/.claude/skills/vue-skilld/.skilld/_BEST_PRACTICES.md` then validate with `skilld validate`.

## Verification

- Confirm all 14 items have `[source](./.skilld/...)` links with section anchors or line references
- Confirm no more than ~3-4 items have code blocks
- Confirm total is under 241 lines
- Run `skilld validate` and fix any warnings
<!-- /skilld:best-practices -->

---
> Source: [synmux/syn-horse](https://github.com/synmux/syn-horse) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
