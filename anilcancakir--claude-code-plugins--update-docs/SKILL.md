---
name: update-docs
description: Update inline documentation for uncommitted code changes. Use after modifying code or when documentation needs sync. Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Documentation Update Task

Update documentation for all uncommitted code changes.

## Steps

1. Run `git diff --name-only` to identify modified files
2. For each code file:
   - Detect existing documentation style (see references/doc-patterns.md)
   - Find new/modified functions, classes, methods
   - Add or update documentation blocks
3. If `/docs` folder exists:
   - Check if changed code has corresponding docs
   - Update affected documentation files
   - Do NOT modify README.md, CHANGELOG.md, LICENSE
4. Report all changes made

## Reference Documents

For documentation patterns by language, read:
- `references/doc-patterns.md` - Format examples for each stack

## Documentation Style Matching

Always match the existing documentation style in the file:
- If PHPDoc exists, use PHPDoc
- If JSDoc exists, use JSDoc
- If no existing docs, detect from file extension

## Output Requirements

Return a report listing:
- Files modified
- Docblocks added/updated
- Summary counts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
