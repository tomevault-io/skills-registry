---
name: project-analyze
description: Scans codebases to generate architecture documentation (ARCHITECTURE.md). Use when joining an existing project, understanding codebase structure, exploring project architecture, or preparing for agent-foreman init. Triggers on 'analyze project', 'understand codebase', 'explore architecture', 'scan project structure', 'survey project'. Use when this capability is needed.
metadata:
  author: mylukin
---

# 🔍 Project Analyze

**One command**: `agent-foreman init --analyze`

## Quick Start

```bash
agent-foreman init --analyze
```

Output: `docs/ARCHITECTURE.md`

## Options

| Flag | Effect |
|------|--------|
| `--analyze-output ./path/FILE.md` | Custom output path |
| `--verbose` | Show detailed progress |

## Use When

- Joining existing project → understand before changing
- Before `agent-foreman init` → faster initialization

## Skip When

- New/empty project → use `agent-foreman init` directly

## Read-Only

No code changes. No commits. Safe to run anytime.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mylukin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
