---
name: implementing-react-i18n
description: Use when adding, fixing, or reviewing React internationalization, language selectors, locale resources, translated UI copy, or bugs where changing language does not update user-facing strings
metadata:
  author: danduma
---

# Implementing React I18n

## Overview

Use the shared i18n library as the source of truth: locale state lives in an `i18nManager`, user-facing strings live in locale JSON resources, call sites render through `t("key", params?)`, and React components subscribe only so language changes repaint.

## First Product Question

When building a meaningful React UI and the user has not mentioned language support, offer it as an option:

> Should we support other languages? If yes, I will wire user-facing copy through i18n resources now instead of hardcoding strings.

If the user says yes, asks for language support, mentions locale/i18n/translation, or reports that language switching only updates part of the UI, use this skill.

## Required Pattern

1. Create or reuse `shared/locales/*.json` as flat dotted-key resources.
2. Create or reuse `src/lib/i18n.ts` with:
   - `i18nManager = createI18nManager(...)`
   - `export const t = i18nManager.t`
   - `export function useI18nSnapshot()` using `useSyncExternalStore`
   - dynamic locale loaders for non-fallback languages
3. Components that render translated text call `useI18nSnapshot()` and render with `t(...)`.
4. Non-React modules use `t(...)` where user-visible text is produced.
5. Store stable ids and translate at render time. Do not persist translated UI copy.

```ts
import { useCallback, useSyncExternalStore } from "react";
import { createI18nManager } from "@danduma/i18n";
import fallbackDictionary from "../../shared/locales/en.json";

export const localeLoaders = {
  es: async () => (await import("../../shared/locales/es.json")).default,
} as const;

export const i18nManager = createI18nManager({
  dictionaries: { en: fallbackDictionary },
  localeLoaders,
  fallbackLocale: "en",
  storageKey: "app-locale",
});

export const t = i18nManager.t;

export function useI18nSnapshot() {
  return useSyncExternalStore(
    useCallback((listener) => i18nManager.subscribe(listener), []),
    useCallback(() => i18nManager.getSnapshot(), []),
    () => i18nManager.getSnapshot(),
  );
}
```

```tsx
import { t, useI18nSnapshot } from "@/lib/i18n";

export function SaveButton() {
  useI18nSnapshot();

  return <button type="button">{t("common.save")}</button>;
}
```

The hook is not the translation API. It exists to subscribe the component. The `t()` function is the translation API.

## What Must Be Translated

Every user-facing frontend string must use `t()`:

- JSX text, button labels, menu items, tab labels, empty states, status labels
- `aria-label`, `title`, `placeholder`, `alt`, helper text, error fallback text
- visible option labels for selects, segmented controls, toggles, and radio groups

Do not translate protocol values, storage keys, API paths, CSS classes, model ids, database values, or enum values unless they are displayed as copy.

## Locale Resource Rules

- Add every new key to the fallback locale and every supported locale in the same change.
- Add a parity test that compares all locale resource keys to the fallback dictionary.
- Use stable dotted keys by surface and intent, for example `settings.runtime.recovery`.
- Interpolate with named params: `t("settings.agents.toggleWorker", { worker: label })`.
- If translations are not approved yet, either support only the fallback language or ask which languages to support. Do not invent broad language support silently.

## Variable Naming

Never name variables with locale codes or locale-like names. Avoid `it`, `en`, `es`, `enUS`, `locIt`, `locEnUs`, or similar names, especially in tests where `it` can shadow the test function.

Use role-based names instead:

- `fallbackDictionary`
- `candidateDictionary`
- `localeDictionaries`
- `resourcesByLocale`
- `supportedLocaleCodes`

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| Importing `t` and never subscribing | Call `useI18nSnapshot()` in every React component that renders translated strings. |
| Returning `{ t }` from a custom hook | Keep `t()` as the direct call-site API; use hooks only for snapshots/subscriptions. |
| Translating only the language selector | Migrate the parent dialog, tabs, labels, placeholders, controls, and error fallbacks too. |
| Using localized labels as state | Store stable ids like `queue`; render `t("settings.runtime.queue")`. |
| Adding English keys only | Update every supported locale and run the key parity test. |

## Verification

Run focused checks after any i18n change:

```bash
pnpm test tests/lib/i18n.test.ts
pnpm test tests/ui/settings-dialog.test.ts
pnpm exec tsc --noEmit
```

For broader migrations, also run relevant UI tests and a browser smoke test: change language, reload, and verify representative labels outside the language selector update.

---
> Source: [danduma/ultrapowers](https://github.com/danduma/ultrapowers) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-11 -->
