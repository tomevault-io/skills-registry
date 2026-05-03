---
name: expand-name-mappings
description: Guides expansion of non-Muslim name mappings in muslimname. Use when adding names for countries, regions, or languages; looking up popular names by culture; or validating name mappings, categories, and language scripts. Use when this capability is needed.
metadata:
  author: codingshot
---

# Expand Name Mappings

## When to Use

- Adding names for a new country or language
- Looking up popular names by region/culture
- Checking language, script, or category for a name
- Validating mappings before commit

## Quick Workflow

1. **Identify target** — Which language/region? (e.g. Polish, Greek, Dutch)
2. **Look up names** — Use `docs/NAME_LOOKUP_GUIDE.md` and Wikipedia "List of most popular given names"
3. **Pick category** — Use [reference.md](reference.md) for language → category mapping
4. **Add mappings** — Follow format in `src/data/nameMapping.ts`, avoid duplicate keys
5. **Validate** — Run `npm run build`, check slugs exist

## Key Files

- **Mappings:** `src/data/nameMapping.ts` — `christianToMuslimNameMapping`, categories, `westernNameVariants`, `chineseCharToPinyin`
- **Slugs:** `src/data/names.ts` — `namesDatabase`, `findNameBySlug`
- **Aliases:** `src/data/slugAliases.ts`
- **Guide:** `docs/NAME_LOOKUP_GUIDE.md`
- **UI:** `src/pages/WesternNamesPage.tsx` — add new categories to filter/stats if adding a language group

## Entry Format

```ts
"keyname": {
  muslimNames: ["slug1", "slug2"],
  meaning: "Original meaning",
  connection: "Bridge to Islamic tradition",
  originalScript?: "字",  // CJK only
  category: "language-male" | "language-female"
}
```

- Keys: lowercase, no diacritics (normalize José → jose)
- No duplicate keys in the object
- Prefer `muslimNames` slugs that exist in `namesDatabase`

## Language & Script Checks

| Language | Key format | Script / notes |
|----------|------------|----------------|
| Chinese | Pinyin (wei, ming) | Add `originalScript` and `chineseCharToPinyin` entry |
| Korean | Romanized, no hyphen (minjun not min-jun) | |
| Japanese | Romanized (hiroshi, sakura) | |
| Portuguese/Spanish | Add diacritic variant if common (joão, josé) | `normalizeDiacritics` handles lookup |
| Russian | Transliteration (aleksandr, dmitry) | |
| Hebrew | Use hebrewOrigin if biblical | |

## New Language Group

1. Add category to `NameMappingCategory` in nameMapping.ts
2. Add names with new category
3. Add category to `WesternNamesPage.tsx` categories array and stats
4. Add badge emoji in Badge replace chain if desired

## Validation

- `npm run build` — must pass, no duplicate key errors
- `findNameBySlug(slug)` — muslimNames should resolve where possible
- New categories must exist in `NameMappingCategory` type

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codingshot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
