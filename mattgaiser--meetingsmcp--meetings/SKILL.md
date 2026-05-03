---
name: meetings
description: AI-powered meeting transcript management. Sync from Recall.ai, search transcripts, generate summaries, track action items, list participants, and query meetings with natural language. Use when this capability is needed.
metadata:
  author: mattgaiser
---

This skill provides meeting transcript management through the MeetingsMCP system.

## Setup

Before using, run setup to configure your API keys:

```bash
cd "${CLAUDE_PLUGIN_ROOT}" && source .venv/bin/activate && python skill/run.py setup
```

## Executing Commands

Run meetings commands using:

```bash
cd "${CLAUDE_PLUGIN_ROOT}" && source .venv/bin/activate && python skill/run.py $ARGUMENTS
```

Where `$ARGUMENTS` is the command and its options.

## Available Commands

| Command | Description |
|---------|-------------|
| `setup` | Check setup status or configure API keys |
| `sync` | Sync new meetings from Recall.ai |
| `search <query>` | Search meeting transcripts |
| `ask <question>` | Ask a natural language question about your meetings |
| `summary <period>` | Generate a summary for a time period |
| `actions` | List action items from meetings |
| `list` | List recent meetings |
| `participants` | List all meeting participants |
| `show <id>` | Show details for a specific meeting |
| `decisions` | Find decisions made in meetings |
| `stats` | Show meeting statistics |
| `auto-sync` | Manage automatic background syncing |
| `help` | Show all available commands |

## Examples

### List all participants
```bash
cd "${CLAUDE_PLUGIN_ROOT}" && source .venv/bin/activate && python skill/run.py participants
```

### List meetings with a specific person
```bash
cd "${CLAUDE_PLUGIN_ROOT}" && source .venv/bin/activate && python skill/run.py list --participant "Sarah"
```

### Search transcripts
```bash
cd "${CLAUDE_PLUGIN_ROOT}" && source .venv/bin/activate && python skill/run.py search "pricing discussion" --date "this week"
```

### Ask a question about meetings
```bash
cd "${CLAUDE_PLUGIN_ROOT}" && source .venv/bin/activate && python skill/run.py ask "What did we decide about the roadmap?"
```

### Generate a weekly summary
```bash
cd "${CLAUDE_PLUGIN_ROOT}" && source .venv/bin/activate && python skill/run.py summary "last week" --focus "product decisions"
```

### Show statistics
```bash
cd "${CLAUDE_PLUGIN_ROOT}" && source .venv/bin/activate && python skill/run.py stats
```

## Date Filters

Supported date filters for `--date` option:
- `today`, `yesterday`
- `this week`, `last week`
- `this month`, `last month`
- `last N days` (e.g., "last 7 days")
- `YYYY-MM-DD` (specific date)
- Month names (e.g., "January", "Feb")

## Requirements

- Python 3.11+
- API keys for: Recall.ai, OpenAI (embeddings), Anthropic (Claude)
- Virtual environment with dependencies installed

Run `python setup.py` for guided installation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mattgaiser) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
