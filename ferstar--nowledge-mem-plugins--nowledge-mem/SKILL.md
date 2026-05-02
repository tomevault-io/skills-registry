---
name: nowledge-mem
description: | Use when this capability is needed.
metadata:
  author: ferstar
---

# Nowledge Mem

Unified CLI for Nowledge Mem knowledge base management.

## Quick Reference

| Command | Description |
|---------|-------------|
| `nm search "..."` | Search memories |
| `nm add "..."` | Add a new memory |
| `nm persist` | Save current session |
| `nm expand <id>` | View full thread |
| `nm update <id>` | Update memory |
| `nm delete <id>` | Delete memory |
| `nm labels` | List all labels |
| `nm diagnose` | Check connectivity |

## Execution

Run all commands via `uv run nm` from the skill directory.

```bash
cd <skill-directory>
uv run nm <command> [options]
```

## Core Workflows

### Search Knowledge Base

```bash
# Basic search
uv run nm search "Python async patterns"

# Verbose with JSON output
uv run nm search "API design" --verbose --json
```

### Add Memory

```bash
# Simple add
uv run nm add "Content to remember"

# With metadata
uv run nm add "Important config" --title "DB Config" --importance 0.8 --labels "config,db"
```

### Persist Current Session

Set `PROJECT_PATH` to the actual project directory before running.

```bash
PROJECT_PATH=/path/to/project uv run nm persist
PROJECT_PATH=/path/to/project uv run nm persist --title "Feature implementation"
```

### Expand Thread

```bash
uv run nm expand <thread_id>
```

## Reference Documentation

For detailed parameters and options:
- Command parameters: `references/command_reference.md`
- Configuration: `references/configuration.md`
- Usage patterns: `references/usage_patterns.md`
- Troubleshooting: `references/troubleshooting.md`

## Trigger Keywords

| Language | Keywords |
|----------|----------|
| 中文 | 记忆, 知识库, 保存, 搜索, 记录, 存储, 会话, 持久化 |
| English | memory, knowledge, save, search, persist, recall, remember, session |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ferstar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
