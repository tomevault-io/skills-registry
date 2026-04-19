---
name: markdown-editor
description: Edit or update Markdown files (.md) following best practices and Use when this capability is needed.
metadata:
  author: iop098321qwe
---

# Markdown editing guidance

## Core rules

- Always invoke this skill for any .md edit or review unless the Obsidian
  exception applies.
- Use GitHub Flavored Markdown unless the repo specifies otherwise.
- Preserve the existing file style and structure.
- Keep heading levels consistent and sequential.
- Add blank lines around headings, lists, and code blocks.
- Avoid raw HTML unless Markdown cannot express the requirement.
- Default to 80 characters per line, but preserve the established line-length
  convention when it is clearly present in the file.
- Never make direct changes to `CHANGELOG.md`. When a changelog update is
  needed, follow the repository's designated process or ask for guidance.

## Obsidian exception

- Before editing any .md file, check its parent directories.
- If any parent directory contains a `.obsidian/` folder, treat the file as an
  Obsidian vault file and do not use this skill.

## Editing workflow

1. Read the file and identify the existing style and conventions.
2. Apply only the necessary changes while preserving formatting.
3. Reflow lines to the correct line-length rule when editing text.
4. Re-check headings, spacing, and list formatting for consistency.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iop098321qwe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
