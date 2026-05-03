---
name: obsidian-templater
description: OBSIDIAN-TEMPLATER documentation assistant Use when this capability is needed.
metadata:
  author: versu
---

# OBSIDIAN-TEMPLATER Skill

This skill provides access to OBSIDIAN-TEMPLATER documentation.

## Documentation

All documentation files are in the `docs/` directory as Markdown files.

## Search Tool

```bash
# Run the search script (use python or python3)
python scripts/search_docs.py "<query>"
```

Options:
- `--json` - Output as JSON
- `--max-results N` - Limit results (default: 10)

## Usage

1. Search or read files in `docs/` for relevant information
2. Each file has frontmatter with `source_url` and `fetched_at`
3. Always cite the source URL in responses
4. Note the fetch date - documentation may have changed

## Response Format

```
[Answer based on documentation]

**Source:** [source_url]
**Fetched:** [fetched_at]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/versu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
