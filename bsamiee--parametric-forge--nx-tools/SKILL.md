---
name: nx-tools
description: >- Use when this capability is needed.
metadata:
  author: bsamiee
---

# [H1][NX-TOOLS]
>**Dictum:** *Uniform interfaces eliminate invocation ambiguity.*

<br>

Query Nx workspace with unified Python CLI.

[IMPORTANT] Zero-arg commands default to `base=main`, `output=.nx/graph.json`.

```bash
# Zero-arg commands
uv run .claude/skills/nx-tools/scripts/nx.py workspace
uv run .claude/skills/nx-tools/scripts/nx.py path
uv run .claude/skills/nx-tools/scripts/nx.py generators
uv run .claude/skills/nx-tools/scripts/nx.py affected
uv run .claude/skills/nx-tools/scripts/nx.py affected --base develop
uv run .claude/skills/nx-tools/scripts/nx.py graph
uv run .claude/skills/nx-tools/scripts/nx.py docs
uv run .claude/skills/nx-tools/scripts/nx.py docs --topic affected
uv run .claude/skills/nx-tools/scripts/nx.py tokens --path CLAUDE.md

# Required-arg commands
uv run .claude/skills/nx-tools/scripts/nx.py project --name parametric-portal
uv run .claude/skills/nx-tools/scripts/nx.py run --target build
uv run .claude/skills/nx-tools/scripts/nx.py run --target typecheck
uv run .claude/skills/nx-tools/scripts/nx.py schema --generator @nx/react:app
```

---
## [1][OUTPUT]

Commands return `{"status": "success|error", ...}`.

| [INDEX] | [CMD]        | [RESPONSE]                            |
| :-----: | ------------ | ------------------------------------- |
|   [1]   | `workspace`  | `{projects: string[]}`                |
|   [2]   | `project`    | `{name: string, project: object}`     |
|   [3]   | `affected`   | `{base: string, affected: string[]}`  |
|   [4]   | `run`        | `{target: string, output: string}`    |
|   [5]   | `generators` | `{generators: string}`                |
|   [6]   | `schema`     | `{generator: string, schema: string}` |
|   [7]   | `graph`      | `{file: string}`                      |
|   [8]   | `docs`       | `{topic: string, docs: string}`       |
|   [9]   | `tokens`     | `{path: string, output: string}`      |
|  [10]   | `path`       | `{path: string}`                      |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bsamiee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
