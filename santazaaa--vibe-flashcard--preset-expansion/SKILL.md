---
name: preset-expansion
description: Add new language presets with JSON data and UI updates. Use when this capability is needed.
metadata:
  author: santazaaa
---

## What I do
- Create JSON files in src/presets/ with word/translation arrays (e.g., [{word: "hello", translation: "你好"}]).
- Update CardList.tsx to add language buttons.
- Modify page.tsx to load and handle new presets.

## When to use me
Use for expanding multi-language support. Provide language name and sample words.

## Examples
For French preset:

Create src/presets/french-lv1.json:

```json
[
  {"word": "bonjour", "translation": "hello"},
  {"word": "merci", "translation": "thank you"}
]
```

Update src/app/PresetCards.tsx to include French in presetOptions and getPresetCards.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/santazaaa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
