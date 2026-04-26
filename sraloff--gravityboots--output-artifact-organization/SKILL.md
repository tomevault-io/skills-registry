---
name: output-artifact-organization
description: Guidelines for saving SQL, JSON, and examples to specific folders. Use when this capability is needed.
metadata:
  author: sraloff
---

# Output Artifact Organization

## When to use this skill
- The user asks to generate SQL, JSON data, or example code files.
- You need to decide WHERE to save a file.

## 1. Standards
- **SQL**: Save to `/sql/`. Filename: `YYYY_MM_DD_description.sql`.
- **JSON**: Save to `/examples/json/`. Filename: `description.json`.
- **Docs**: Save to `/docs/` or specific subdirectory.
- **Temporary**: Avoid creating files in root; use a temp folder if it's throwaway.

## 2. Naming
- Use descriptive, kebab-case logic.
- Include date prefixes for migrations or chronologically significant files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sraloff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
