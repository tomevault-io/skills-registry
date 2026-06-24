---
name: memories
description: Save and retrieve memories or embeddings via the repo helpers or API. Use when working with embedding config or memory storage. Use when this capability is needed.
metadata:
  author: proompteng
---

# Memories

## Overview

Use the memories helpers to store and retrieve embeddings in a consistent way. Ensure embedding dimension matches the DB schema.

## Save

```bash
bun run --filter memories save-memory   --task-name bumba-enrich-repo   --content "Enrichment facts for services/bumba"   --summary "Bumba repo facts"   --tags "bumba,enrich"
```

## Retrieve

```bash
bun run --filter memories retrieve-memory   --query "enrichFile workflow"   --limit 5
```

## Environment

- `OPENAI_API_BASE_URL` / `OPENAI_API_BASE`
- `OPENAI_EMBEDDING_MODEL`
- `OPENAI_EMBEDDING_DIMENSION`
- `OPENAI_EMBEDDING_TIMEOUT_MS`

## Resources

- Reference: `references/memories-runbook.md`
- Helper: `scripts/memories.sh`
- Example payload: `assets/memory-entry.json`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/proompteng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
