---
name: adding-translations
description: >- Use when this capability is needed.
metadata:
  author: franklinogf
---

# Adding Translations

## When to Apply

Activate this skill when:

- Modifying language files such as `lang/es.json` or any other language file in the `lang` directory.
- When the user mentions adding translations, updating language files, or working with localization in general.

## Important Note

- When adding new translations to `lang/es.json` or any other language file, you must run `php artisan utils:ts-lang-keys` to regenerate the TypeScript translation types.
- This ensures type safety for the translation keys in the frontend code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/franklinogf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
