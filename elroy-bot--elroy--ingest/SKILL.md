---
name: ingest
description: Ingest documents into Elroy memory Use when this capability is needed.
metadata:
  author: elroy-bot
---

Ingest documents into Elroy's memory system for later recall.

When the user invokes this skill with `/ingest [PATH]`, ingest the document(s) by running:

```bash
elroy ingest "$ARGUMENTS"
```

This can ingest:
- Single files: `/ingest README.md`
- Directories: `/ingest docs/` (use with `-r` flag for recursive)
- Multiple formats: Markdown, PDF, text files, etc.

Options:
- `-f, --force-refresh` - Re-ingest even if already ingested
- `-r, --recursive` - Recursively ingest directories

Examples:
- `/ingest README.md`
- `/ingest docs/ -r`
- `/ingest specs/api-documentation.pdf`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elroy-bot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
