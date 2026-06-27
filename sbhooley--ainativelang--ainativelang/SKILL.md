---
name: ainl
description: Deterministic compiled graphs for OpenClaw (importer, runtime, MCP tools) Use when this capability is needed.
metadata:
  author: sbhooley
---

# AINL for OpenClaw

**AINL** is an AI-native language that compiles **deterministic graphs**. In [OpenClaw](https://openclaw.ai/), use it to import workflows, compile them, run them, and expose **`ainl-mcp`** tools to the agent via **`~/.openclaw/openclaw.json`**.

**Package:** `ainativelang` **v1.8.0** (this tree; **PyPI** after publish — **`docs/RELEASING.md`**). Optional extras: `[mcp]`, `[solana]`.

## Agent instructions

Follow this skill when the user is on **OpenClaw** (or wants **deterministic `.ainl` graphs** with **import / compile / run / MCP**). Prefer **`./install.sh`** from a checked-out skill folder, or the manual **`pip install 'ainativelang[mcp]' && ainl install-mcp --host openclaw`** path (alias **`ainl install-openclaw`**). OpenClaw does **not** use `zeroclaw skills install <url>` — install via **ClawHub** (if available) or **copy this folder** into **`~/.openclaw/skills`** or **`<workspace>/skills`**. After bootstrap, suggest the **morning briefing** prompt example below when it fits.

## When to use this skill

- The user wants **portable, compiled workflows** (`.ainl`) instead of ad-hoc prompts.
- They mention **import**, **compile**, **morning briefing**, **clawflows**, **ecosystem**, or **deterministic graphs**.
- They need **MCP** access to validation / compile / ecosystem listing (`ainl-mcp`).

## Install (pick one)

1. **From this skill directory (after ClawHub or manual copy):** run `./install.sh`  
   That optionally refreshes the **OpenClaw CLI** via npm, upgrades **`ainl[mcp]`**, and runs **`ainl install-mcp --host openclaw`** (MCP merge into **`openclaw.json`**, **`~/.openclaw/bin/ainl-run`** wrapper).

2. **Manual (no skill checkout):**  
   `pip install 'ainativelang[mcp]' && ainl install-mcp --host openclaw`

## Commands the user cares about

| Command | Purpose |
|--------|---------|
| `ainl import …` | Import Markdown or ecosystem sources into `.ainl` |
| `ainl compile …` | Compile / validate graphs |
| `ainl run …` | Execute a graph via the CLI runtime where applicable |
| `~/.openclaw/bin/ainl-run <file.ainl>` | After `install-mcp --host openclaw`: wrapper that compiles then runs |

## After install — prompt suggestion

Tell the user they can say in OpenClaw:

> Import the morning briefing using AINL.

(They should point the importer at their Markdown or pack; `ainl import` subcommands match their source type.)

## MCP

Configure the host so **`mcp.servers.ainl`** in **`~/.openclaw/openclaw.json`** runs **`ainl-mcp`** as a stdio MCP server (see AINL docs: *OpenClaw integration* / *External orchestration*). **`ainl install-mcp --host openclaw`** merges that entry when missing.

## References

- AINL: [github.com/sbhooley/ainativelang](https://github.com/sbhooley/ainativelang)
- **Gold standard (profiles, caps, cron, bootstrap, verification):** [docs/operations/OPENCLAW_AINL_GOLD_STANDARD.md](https://github.com/sbhooley/ainativelang/blob/main/docs/operations/OPENCLAW_AINL_GOLD_STANDARD.md) — `openclaw_ainl_gold_standard`
- **Host briefing — AINL v1.8.0 (what ships vs what OpenClaw must wire; doc key `openclaw_host_ainl_1_2_8`):** [docs/operations/OPENCLAW_HOST_AINL_1_2_8.md](https://github.com/sbhooley/ainativelang/blob/main/docs/operations/OPENCLAW_HOST_AINL_1_2_8.md) — `openclaw_host_ainl_1_2_8`
- OpenClaw: [openclaw.ai](https://openclaw.ai/)
- Package: **`ainativelang`** **v1.8.0** (this tree; **PyPI** after publish — **`docs/RELEASING.md`**)

---
> Source: [sbhooley/ainativelang](https://github.com/sbhooley/ainativelang) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
