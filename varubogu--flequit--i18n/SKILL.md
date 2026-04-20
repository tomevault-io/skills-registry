---
name: i18n
description: Implement and maintain internationalization with Inlang Paraglide, including message updates and reactive usage patterns. Use when this capability is needed.
metadata:
  author: varubogu
---

# i18n Skill

Use this skill when adding/updating localized text.

## Core files

- `project.inlang/messages/en.json`
- `project.inlang/messages/ja.json`
- Generated output under `src/paraglide/`

## Workflow

1. Add/update message keys in `en.json` and `ja.json`.
2. Optionally run machine translation for draft text.
3. Build to regenerate typed messages.
4. Update UI usage with reactive message patterns.

## Commands

- Optional draft translation: `bun run machine-translate`
- Regenerate typed messages: `bun run build`

## Usage rules

- Keep keys stable and descriptive.
- Prefer typed message access from generated APIs.
- Ensure tests that depend on translation set up deterministic mocks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/varubogu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
