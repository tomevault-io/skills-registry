---
name: vue-i18n8-to-v9
description: Use when upgrading vue-i18n v8 to v9+ in Vue 3, including createI18n setup, legacy/composition mode decisions, and translation API behavior changes.
metadata:
  author: JuanJoseGonGi
---

# Vue I18n 8 to 9

## Overview

Use this skill to migrate i18n bootstrapping and translation usage safely.

## Key Breaking Changes

- `new VueI18n(...)` -> `createI18n(...)`
- install via `app.use(i18n)`
- `dateTimeFormats` -> `datetimeFormats`
- translation APIs now return strings only
- component name and prop changes (`i18n` -> `i18n-t`, `path` -> `keypath`)

## Migration Strategy

1. Start in legacy mode for parity
   - Keep Options API `$t` usage working first.
   - Migrate bootstrap with `createI18n`.

2. Migrate message API edge cases
   - Replace object/array retrieval via `$t` with `tm` + `rt` patterns where needed.

3. Introduce composition usage incrementally
   - Use `useI18n()` in new or refactored components.

4. Normalize component interpolation
   - Update translation components and slot usage.

## Bootstrap Example

```ts
import { createI18n } from "vue-i18n"

export const i18n = createI18n({
  legacy: true,
  locale: "en",
  fallbackLocale: "en",
  messages,
})
```

## Done Criteria

- i18n is created with `createI18n` and installed via app plugin API.
- No v8-only constructor usage remains.
- All translation-rendering paths pass UI checks across supported locales.

## Common Pitfalls

- Assuming `$t` still returns arrays/objects.
- Mixing composition and legacy usage without migration boundaries.
- Missing rename of `datetimeFormats`, causing silent format issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/JuanJoseGonGi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
