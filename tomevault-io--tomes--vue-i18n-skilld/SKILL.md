---
name: vue-i18n-skilld
description: Internationalization plugin for Vue.js. ALWAYS use when writing code importing \"vue-i18n\". Consult for debugging, best practices, or modifying vue-i18n, vue i18n. Use when this capability is needed. Use when this capability is needed.
metadata:
  author: tomevault-io
---

# intlify/vue-i18n `vue-i18n@11.4.0`
**Tags:** rc: 9.0.0-rc.9, alpha: 9.2.0-alpha.9, legacy: 8.28.2

**References:** [Docs](./references/docs/_INDEX.md)
## API Changes

This section documents version-specific API changes — prioritize recent major/minor releases.

- DEPRECATED: Legacy API mode — deprecated in v11 for Composition API preference; scheduled for removal in v12 [source](./references/releases/v11.0.0.md#deprecate-legacy-api-mode)

- DEPRECATED: Custom Directive `v-t` — deprecated in v11 due to limited optimization benefits in Vue 3; scheduled for removal in v12 [source](./references/releases/v11.0.0.md#deprecate-custom-directive-v-t)

- BREAKING: `tc` and `$tc` — dropped in v11 for Legacy API mode; use pluralization support in `t` and `$t` instead [source](./references/releases/v11.0.0.md#drop-tc-and-tc-for-legacy-api-mode)

- NEW: Vue 3 Vapor Mode — added compatibility for Vue 3 vapor mode in v11.2.0 [source](./references/releases/v11.2.0.md#features)

- BREAKING: JIT Compilation — enabled by default in v10 to solve CSP issues and support dynamic resources [source](./references/docs/guide/migration/breaking10.md#default-enable-for-jit-compilation)

- BREAKING: `$t` and `t` Signatures — Legacy API mode signatures changed in v10 to match Composition API mode; positional locale args now require options object [source](./references/docs/guide/migration/breaking10.md#change-t-and-t-overloaded-signature-for-legacy-api-mode)

- NEW: Generated Locale Types — v10 adds support for extending locale types via `GeneratedTypeConfig` for better TypeScript inference [source](./references/releases/v10.0.0.md#support-for-generated-locale-types)

- BREAKING: Modulo `%` Syntax — named interpolation using modulo syntax dropped in v10; use standard `{}` interpolation [source](./references/docs/guide/migration/breaking10.md#drop-modulo-syntax)

- BREAKING: `vue-i18n-bridge` — dropped in v10 following Vue 2 EOL [source](./references/docs/guide/migration/breaking10.md#drop-vue-i18n-bridge)

- BREAKING: `allowComposition` Option — dropped in v10; was previously used for Legacy to Composition API migration on v9 [source](./references/docs/guide/migration/breaking10.md#drop-allowcomposition-option)

**Also changed:** `petite-vue-i18n` GA v10 · Configurable `$i18n` type new v11.1.0 · `mode` property deprecated v11 · `tm` accepts `DefineLocaleMessage` key type v11.0.0 · Part options support `$n` & `$d` new v11.1.4

## Best Practices

- Prefer Composition API mode (`legacy: false`) for all new projects — Legacy API mode is deprecated in v11 and will be removed in v12 [source](./references/docs/guide/migration/breaking11.md#deprecate-legacy-api-mode)

- Use `t()` function or `<i18n-t>` component over the `v-t` directive — the directive is deprecated in v11 and lacks IDE support for key completion [source](./references/docs/guide/migration/breaking11.md#deprecate-custom-directive-v-t)

- Define global resource schemas using `DefineLocaleMessage`, `DefineDateTimeFormat`, and `DefineNumberFormat` interfaces — enables automatic type inference and key completion in `useI18n` without passing type parameters [source](./references/docs/guide/advanced/typescript.md#global-resource-schema-type-definition)

- Use `rt()` (Resolve Translation) when processing locale messages retrieved via `tm()` — ensures proper resolution of nested structures and pluralization for programmatically accessed messages

- Enable `escapeParameter: true` when using `v-html` with translations containing user input — prevents XSS by escaping interpolation parameters and neutralizing dangerous HTML attributes

- Explicitly configure `__VUE_I18N_FULL_INSTALL__` and `__VUE_I18N_LEGACY_API__` feature flags — setting these to `false` in bundler configuration enables better tree-shaking and reduces bundle size [source](./references/docs/guide/advanced/optimization.md#reduce-bundle-size-with-tree-shaking)

- Pre-compile locale messages using `@intlify/unplugin-vue-i18n` — improves performance by using AST/Functions and ensures CSP compliance by avoiding `eval` during runtime compilation [source](./references/docs/guide/advanced/optimization.md#performance)

- Implement lazy loading for locale messages using dynamic `import()` and `setLocaleMessage()` — reduces initial bundle size by loading language resources only when needed (e.g., in router guards) [source](./references/docs/guide/advanced/lazy.md#lazy-loading)

- Synchronize the `html` `lang` attribute and `Accept-Language` headers when switching locales — ensures accessibility (screen readers) and consistent language handling for server-side requests [source](./references/docs/guide/advanced/lazy.md#lazy-loading)

---
> Source: [skilld-dev/vue-ecosystem-skills](https://github.com/skilld-dev/vue-ecosystem-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->

---
> Source: [tomevault-io/tomes](https://github.com/tomevault-io/tomes) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-29 -->
