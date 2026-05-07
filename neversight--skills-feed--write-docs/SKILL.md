---
name: write-docs
description: Write AI-scannable technical documentation. Use when this capability is needed.
metadata:
  author: neversight
---

# Write Documentation

Documentation that is scannable, consistent, and actionable for AI agents.

## Structure

- Max 150 lines per file, one concept per file
- Start with `description:` in YAML frontmatter
- Add TL;DR section at top with most-needed info

## Content

- No duplicates (define once, link elsewhere)
- Use tables for structured data (parameters, config)
- Concrete examples for everything (copy-pasteable)
- Link to real code as templates

## Naming

| Pattern | Use For | Example |
|---------|---------|---------|
| `README.md` | Directory overview | `docs/README.md` |
| `{noun}.md` | Reference | `entities.md` |
| `{verb}-{noun}.md` | How-to | `add-entity.md` |

## Tips

- Use consistent terms (one term per concept)
- Group by task ("How to add X") not system ("X overview")
- Include troubleshooting for common errors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
