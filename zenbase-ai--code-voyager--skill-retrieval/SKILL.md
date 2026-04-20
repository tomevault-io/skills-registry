---
name: skill-retrieval
description: | Use when this capability is needed.
metadata:
  author: zenbase-ai
---

# Skill Retrieval System

ColBERT-based semantic search over skill libraries.

## Quick Start

```bash
# Build the index (one-time, ~30s per skill with LLM analysis)
voyager skill index --verbose

# Search for skills
voyager skill find "resume where we left off"
voyager skill find "what should I work on next"
voyager skill find "turn this workflow into a skill"
```

## CLIs

### `voyager skill index`

Build or update the skill search index.

```bash
voyager skill index [OPTIONS]
```

Options:
- `--paths PATH` — Additional skill directories to index
- `--output DIR` — Index location (default: `~/.skill-index/`)
- `--rebuild` — Force rebuild from scratch
- `--skip-llm` — Skip LLM analysis (faster but lower quality)
- `-v, --verbose` — Show progress

### `voyager skill find`

Search for relevant skills.

```bash
voyager skill find "your query" [OPTIONS]
```

Options:
- `-k, --top-k N` — Number of results (default: 5)
- `--index DIR` — Index location
- `--json` — Output as JSON

## Index Locations

- Default: `~/.skill-index/`
- Override via `VOYAGER_SKILL_INDEX_PATH` env var
- Local dogfooding: `--output ./.claude/voyager/skill-index/`

## Skill Sources

Auto-discovers skills from:
- Plugin skills: `./skills/`
- Local mirror: `./.claude/skills/local/`
- Generated skills: `./.claude/skills/generated/`
- User skills: `~/.claude/skills/`

Override with `CLAUDE_SKILLS_PATH` env var.

## How It Works

1. **Discovery**: Find all `SKILL.md` files in known locations
2. **Analysis**: Use LLM to extract metadata (purpose, triggers, capabilities)
3. **Embedding**: Generate searchable text from metadata
4. **Indexing**: Build ColBERT index for fast semantic search

Indexing happens once; search is instant (no LLM calls).

See `reference.md` for implementation details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zenbase-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
