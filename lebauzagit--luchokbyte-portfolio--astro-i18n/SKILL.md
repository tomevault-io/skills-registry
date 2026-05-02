---
name: astro-i18n
description: Update UI copy and labels in the Astro i18n system (src/i18n/ui.ts and src/i18n/utils.ts). Use when adding or changing UI text in multiple languages. Use when this capability is needed.
metadata:
  author: lebauzagit
---

# Astro i18n

Use this skill for UI text changes that require localization.

## Scope
- UI strings: src/i18n/ui.ts
- Utilities: src/i18n/utils.ts
- Pages: src/pages (localized variants when required)

## Workflow
1. Locate the key(s) in src/i18n/ui.ts.
2. Add or update values for all supported locales.
3. Verify usage in components or pages.
4. If necessary, update page-level copy in localized routes.

## Requirements
- Keep keys stable and descriptive.
- Ensure all locales have values.
- Preserve existing formatting and style.

## Output
- List updated keys and locales.
- Mention any components/pages touched.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lebauzagit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
