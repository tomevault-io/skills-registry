---
name: flutter-localization
description: Use this skill whenever generating, editing, or refactoring Flutter UI code to ensure text is properly localized using AppLocalizations instead of hardcoded strings.
metadata:
  author: jl115
---

# Flutter UI and Localization Rules

When working on Flutter UI components, you must follow these rules strictly:

1. **No Hardcoded Strings:** NEVER use hardcoded static strings for user-facing text (e.g., `Text('Search')` is strictly forbidden).
2. **Use AppLocalizations:** ALWAYS use the app's localization class: `AppLocalizations.of(context)!.string_key`.
3. **Naming Convention:** Format all localization keys in `snake_case`.
4. **Supported Languages and Files:** This application supports the following languages. You must use these exact files for translations:
   - English: `lib/core/localization/app_en.arb`
   - German: `lib/core/localization/app_de.arb`
   - Italian: `lib/core/localization/app_it.arb`
   - French: `lib/core/localization/app_fr.arb`
   - Romansh: `lib/core/localization/app_ro.arb`
5. **Direct File Editing Requirement:** If a completely new string is needed, DO NOT just print the JSON in our chat. You MUST directly modify all the `.arb` files listed above to insert the new translated key-value pairs into the JSON structures. Ensure the JSON remains perfectly valid.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jl115) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
