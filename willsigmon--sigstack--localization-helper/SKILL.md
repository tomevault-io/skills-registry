---
name: localization-helper
description: Find hardcoded strings in Leavn app, add I18n.str() calls, create localization keys for all 6 supported languages (en, es, de, zh-Hans, ar, he) Use when this capability is needed.
metadata:
  author: willsigmon
---

# Localization Helper

Replace hardcoded Text() strings with I18n pattern for 6 languages.

Pattern:
```swift
// BEFORE
Text("Verse of the Day")

// AFTER
Text(I18n.str("widget.verseOfDay", default: "Verse of the Day"))
```

Add keys to: `LeavnApp/Sources/Resources/{lang}.lproj/Localizable.strings`

Use when: Hardcoded strings found, adding UI text, widget/ChatterboxKit localization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willsigmon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
