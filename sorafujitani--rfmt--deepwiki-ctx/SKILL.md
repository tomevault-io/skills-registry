---
name: deepwiki-ctx
description: DEEPWIKI-CTX documentation assistant Use when this capability is needed.
metadata:
  author: sorafujitani
---

# DEEPWIKI-CTX Skill

This skill provides access to DEEPWIKI-CTX documentation.

## Documentation

All documentation files are in the `docs/` directory as Markdown files.

## Search Tool

```bash
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
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sorafujitani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
