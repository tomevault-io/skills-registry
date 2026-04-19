---
name: check-platform-code
description: Audit platform-specific code in androidMain and iosMain. Finds expect/actual declarations and identifies code that could be moved to commonMain. Use when this capability is needed.
metadata:
  author: akshay2211
---

Audit platform-specific code in the DocuBox KMP project.

Steps:

1. Find all `expect` declarations in commonMain:
   - Search for `expect class`, `expect fun`, `expect val`, `expect object` in `composeApp/src/commonMain/`

2. Find all `actual` implementations:
   - Search `composeApp/src/androidMain/` for `actual` declarations
   - Search `composeApp/src/iosMain/` for `actual` declarations

3. Find platform-specific code that isn't part of expect/actual:
   - List all Kotlin files in `androidMain` and `iosMain`
   - Identify any logic beyond the `actual` implementations

4. Analyze and report:
   - List each expect/actual pair and its purpose
   - Flag any code in platform source sets that could move to commonMain
   - Flag any missing `actual` implementations (expect without matching actual)
   - Flag any use of deprecated platform APIs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akshay2211) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
