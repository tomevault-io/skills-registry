---
name: cass
description: >- Use when this capability is needed.
metadata:
  author: jamierumbelow
---

# cass: searching agent history

cass indexes conversations from every local coding agent into a single
searchable corpus. Before solving a problem from scratch, check whether any
agent has already solved something similar.

Never run bare `cass` -- it launches an interactive TUI. Always use `--robot`
or `--json`.

## When to use cass

- You are about to tackle a bug or feature and want to know if a previous
  session already dealt with it.
- Jamie asks "have we done this before?" or "what did we try last time?"
- You need context from a prior debugging session, design discussion, or
  decision.
- You want to understand the history of a particular file, module, or concept
  across agents and projects.

Do not use cass for general web search or documentation lookup. It only
searches local agent session history.

## Health check

Before your first search in a session, confirm the index is healthy:

```bash
cass health --json
```

Exit 0 means the index is fine. Non-zero means you should rebuild:

```bash
cass index --full
```

## Searching

The core command:

```bash
cass search "<query>" --robot --limit 5
```

Queries support boolean operators (`AND`, `OR`, `NOT` / `-`), exact phrases
(`"like this"`), and prefix wildcards (`auth*`). Default behaviour is AND
across terms.

### Useful flags

| Flag | What it does |
|------|--------------|
| `--robot` / `--json` | Machine-readable JSON (required) |
| `--limit N` | Cap result count |
| `--fields minimal` | Only `source_path`, `line_number`, `agent` -- saves tokens |
| `--fields summary` | Adds `title` and `score` |
| `--agent NAME` | Filter to one agent (`claude`, `codex`, `cursor`, etc.) |
| `--workspace PATH` | Filter to a specific project directory |
| `--today` / `--week` / `--days N` | Time filters |
| `--since DATE --until DATE` | Date range (flexible formats: ISO, relative like `-7d`) |
| `--aggregate agent,workspace` | Server-side counts instead of full results |
| `--max-tokens N` | Soft token budget for output (~4 chars/token) |
| `--mode lexical\|semantic\|hybrid` | Search mode (default: lexical) |

### Reading results

Search hits include `source_path` and `line_number`. To see the full context
around a hit:

```bash
cass view /path/to/session.jsonl -n 42 --json
cass expand /path/to/session.jsonl -n 42 -C 5 --json
```

`expand` gives you N messages of context either side of the hit.

### Aggregation

When you need an overview rather than specific hits, aggregate server-side:

```bash
cass search "error" --json --aggregate agent
cass search "*" --json --aggregate agent,workspace,date
```

This returns counts per bucket and is dramatically cheaper in tokens than
fetching full results.

## Search strategy

Start broad, then narrow. A good pattern:

1. **Broad search** to see if anything relevant exists:
   `cass search "auth timeout" --robot --limit 3 --fields summary`

2. **Narrow by project or agent** if you get too many results:
   `cass search "auth timeout" --robot --workspace /path/to/project --days 14`

3. **Expand context** around the best hit:
   `cass expand /path/to/session.jsonl -n 42 -C 5 --json`

Use `--fields minimal` when scanning and `--fields summary` or full output
when you have found something worth reading.

Semantic mode (`--mode semantic`) is useful when you are searching by concept
rather than exact terms -- e.g. "how to handle user login" rather than
"authentication". Hybrid mode combines both.

## Output conventions

- stdout is data only (JSON when `--robot` or `--json` is set)
- stderr is diagnostics
- exit 0 means success

Errors include structured JSON with `code`, `message`, `hint`, and `retryable`
fields. If `retryable` is true, try again; otherwise, follow the `hint`.

## What cass knows about

cass aggregates sessions from: Claude Code, Codex, Gemini CLI, Cline,
OpenCode, Amp, Cursor, ChatGPT, Aider, Pi-Agent, and Factory (Droid). Each
conversation is normalised into a common schema with agent, workspace,
timestamps, and message content.

## Self-documenting API

If you need more detail than this skill provides, cass can explain itself:

```bash
cass robot-docs guide       # quickstart
cass robot-docs commands    # full flag reference
cass robot-docs examples    # copy-paste invocations
cass robot-docs schemas     # JSON response shapes
cass capabilities --json    # feature discovery
```

---
> Source: [jamierumbelow/agentfiles](https://github.com/jamierumbelow/agentfiles) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
