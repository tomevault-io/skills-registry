---
name: context7-tools
description: >- Use when this capability is needed.
metadata:
  author: bsamiee
---

# [H1][CONTEXT7-TOOLS]
>**Dictum:** *Two-step resolution ensures accurate documentation retrieval.*

<br>

Query Context7 library documentation via unified Python CLI.

[IMPORTANT] Commands require arguments. First `resolve`, then `docs`.

```bash
# Resolve library name → ID
uv run .claude/skills/context7-tools/scripts/context7.py resolve --library "react"

# Fetch documentation by resolved ID
uv run .claude/skills/context7-tools/scripts/context7.py docs --library-id "/facebook/react"

# Apply topic filter
uv run .claude/skills/context7-tools/scripts/context7.py docs --library-id "/facebook/react" --topic "hooks"

# Set custom token limit
uv run .claude/skills/context7-tools/scripts/context7.py docs --library-id "/vercel/next.js" --tokens 10000
```

---
## [1][OUTPUT]

Commands return: `{"status": "success|error", ...}`.

| [INDEX] | [CMD]     | [RESPONSE]                             |
| :-----: | --------- | -------------------------------------- |
|   [1]   | `resolve` | `{library: string, matches: object[]}` |
|   [2]   | `docs`    | `{library_id: string, docs: object}`   |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bsamiee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
