---
name: qmd
description: Search and retrieve markdown documents from local knowledge bases using qmd. Supports BM25 keyword search, vector semantic search, and hybrid search with LLM re-ranking. Use for querying indexed notes, documentation, meeting transcripts, and any markdown-based knowledge. Use when this capability is needed.
metadata:
  author: dpaluy
---

# QMD - Local Markdown Search

Search and retrieve documents from locally indexed markdown knowledge bases.

## Installation

```bash
git clone https://github.com/tobi/qmd && cd qmd && bun install -g .
```

## Setup (Strict Order Required)

1. **Add collection first** (required before embed):
   ```bash
   qmd collection add ~/notes --name notes
   ```

2. **Create embeddings** (enables vsearch/query):
   ```bash
   qmd embed
   ```

   First run downloads ~2.5GB of models to `~/.cache/qmd/models/`

3. **Verify setup**:
   ```bash
   qmd status
   ```

## Usage Rules

**Always use `--json` flag** for structured output when invoking qmd commands programmatically.

## Search Commands

### search (BM25 keyword search - fastest)

```bash
qmd search "authentication flow" --json
qmd search "error handling" -n 10 --json
qmd search "config" -c notes --json
```

### vsearch (vector semantic search)

```bash
qmd vsearch "how does login work" --json
qmd vsearch "authentication best practices" -n 20 --json
```

### query (hybrid with LLM re-ranking - best quality)

```bash
qmd query "implementing user auth" --json
qmd query "deployment process" --min-score 0.5 --json
```

### Search Options

| Option | Description |
|--------|-------------|
| `-n NUM` | Number of results (default: 5, or 20 with --json/--files) |
| `-c, --collection NAME` | Restrict to specific collection |
| `--min-score NUM` | Minimum score threshold |
| `--full` | Return complete document content |
| `--all` | Return all matches |

### Output Formats

| Format | Flag | Use Case |
|--------|------|----------|
| Snippets | (default) | Human reading |
| JSON | `--json` | Parsing in code |
| Files only | `--files` | Paths + scores only |
| Markdown | `--md` | Documentation |
| CSV | `--csv` | Spreadsheet export |

## Retrieval Commands

### get (single document)

```bash
qmd get docs/guide.md --json
qmd get notes/meeting.md:50 -l 100 --json
qmd get "#a1b2c3" --json              # Hash from search results
```

### multi-get (multiple documents)

```bash
qmd multi-get "docs/*.md" --json
qmd multi-get "api.md, guide.md" --json
qmd multi-get "notes/**/*.md" --max-bytes 20480 --json
```

Hash references (e.g., `#a1b2c3`) are chunk identifiers shown in search output.

## Maintenance Commands

```bash
qmd status              # Check index health and collection stats
qmd update              # Re-index changed files
qmd update --pull       # Git pull first, then re-index
qmd collection list     # List all collections
qmd collection remove NAME  # Remove a collection
qmd cleanup             # Remove orphaned data, vacuum DB
```

## Search Mode Selection

| Mode | Speed | Quality | Best For |
|------|-------|---------|----------|
| search | Fast | Good | Exact keywords, known terms |
| vsearch | Medium | Better | Conceptual queries, synonyms |
| query | Slow | Best | Complex questions, uncertain terms |

## Performance Notes

- **First run**: Downloads ~2.5GB models (one-time, to `~/.cache/qmd/models/`)
- **Model load**: 5-10 seconds when models already cached
- **search**: No model needed, instant
- **vsearch/query**: Require embedding model loaded

## MCP Server

qmd can run as an MCP server:

```bash
qmd mcp
```

Exposes tools: `qmd_search`, `qmd_vsearch`, `qmd_query`, `qmd_get`, `qmd_multi_get`, `qmd_status`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dpaluy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
