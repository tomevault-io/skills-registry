---
name: mcp-converter
description: Converts MCP servers to Claude Skills to save tokens. Runs the introspection tool to generate skill wrappers.
metadata:
  author: neversight
---

# MCP-to-Skill Converter

## 🚀 Usage

### 1. List Available MCP Servers

See which servers are configured in your `.mcp.json`:

```bash
python .claude/tools/mcp-converter/mcp_analyzer.py --list
```

### 2. Convert a Server

Convert a specific MCP server to a Skill:

```bash
python .claude/tools/mcp-converter/mcp_analyzer.py --server <server_name>
```

### 3. Batch Conversion (Catalog)

Convert multiple servers based on rules:

```bash
python .claude/tools/mcp-converter/batch_converter.py
```

## ℹ️ How it Works

1.  **Introspect**: Connects to the running MCP server.
2.  **Analyze**: Estimates token usage of tool schemas.
3.  **Generate**: Creates a `SKILL.md` wrapper that creates dynamic tool calls only when needed.

## 🔧 Dependencies

Requires `mcp` python package:

```bash
pip install mcp
```

## Memory Protocol (MANDATORY)

**Before starting:**
Read `.claude/context/memory/learnings.md`

**After completing:**

- New pattern -> `.claude/context/memory/learnings.md`
- Issue found -> `.claude/context/memory/issues.md`
- Decision made -> `.claude/context/memory/decisions.md`

> ASSUME INTERRUPTION: If it's not in memory, it didn't happen.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
