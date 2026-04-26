---
name: exa-tools
description: >- Use when this capability is needed.
metadata:
  author: bsamiee
---

# [H1][EXA-TOOLS]
>**Dictum:** *Semantic search surfaces relevant content.*

<br>

Execute Exa AI search queries via unified Python CLI.

[IMPORTANT] Commands require `--query`. API key auto-injected via 1Password.

```bash
# Web search (default: auto type, 8 results)
uv run .claude/skills/exa-tools/scripts/exa.py search --query "Vite 7 new features"

# Neural search for concepts
uv run .claude/skills/exa-tools/scripts/exa.py search --query "Effect-TS best practices" --type neural

# Keyword search with more results
uv run .claude/skills/exa-tools/scripts/exa.py search --query "React 19 release notes" --type keyword --num-results 15

# Code context search (GitHub)
uv run .claude/skills/exa-tools/scripts/exa.py code --query "React useState hook examples"

# Code search with custom results
uv run .claude/skills/exa-tools/scripts/exa.py code --query "Effect pipe examples" --num-results 20
```

---
## [1][OUTPUT]

Commands return: `{"status": "success|error", ...}`.

| [INDEX] | [CMD]    | [RESPONSE]                     |
| :-----: | -------- | ------------------------------ |
|   [1]   | `search` | `{query: string, results: []}` |
|   [2]   | `code`   | `{query: string, context: []}` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bsamiee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
