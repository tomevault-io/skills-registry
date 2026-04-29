---
name: token-saver
description: Reduce OpenClaw AI costs by optimizing workspace files, model selection, and chat compaction. Analyzes .md files for compression, audits AI model usage, and recommends chat compaction thresholds. Writes to AGENTS.md for persistent mode. Accesses home directory to read OpenClaw config and session history. Use when this capability is needed.
metadata:
  author: demerzels-lab
---

# Token Saver

> **💡 Did you know?** Every time you send a prompt, your workspace files (SOUL.md, USER.md, MEMORY.md, AGENTS.md, and more) are sent along with it — every single time. These files count toward your context window, slowing down responses and costing you real money on every message.

Token Saver compresses these files using AI-efficient notation that preserves all your data while making everything lighter, faster, and cheaper.

## Commands

| Command | What it does |
|---|---|
| `/optimize` | Full audit dashboard — files, models, compaction |
| `/optimize tokens` | Compress workspace files (auto-backup) |
| `/optimize compaction` | Chat compaction control |
| `/optimize compaction 120` | Set custom compaction threshold |
| `/optimize revert` | Restore files from backups, disable persistent mode |

## Features

### 📁 Workspace File Compression
Scans all `.md` files sent with each API call. Shows token count and potential savings per file.

**File-aware compression:**
- **SOUL.md** — Light compression, keeps evocative/personality language
- **AGENTS.md** — Medium compression, dense instructions
- **USER.md / MEMORY.md** — Heavy compression, key:value data
- **PROJECTS.md** — No compression (user-specific structure)

### 🤖 Model Audit
Detects current AI models for main chat, cron jobs, and subagents. Suggests cheaper alternatives where appropriate (e.g., Gemini free tier for background tasks).

### 💬 Chat Compaction
Analyzes your conversation history (default: last 7 days) to recommend optimal compaction threshold.

**How it works:**
- Scans your sessions to detect topic change patterns
- Recommends threshold based on YOUR usage
- Shows savings estimate per preset

**Presets:**
- 🟢 Safe (160K) — ~$30/mo savings
- 🟡 Balanced (120K) — ~$100/mo savings  
- 🔴 Aggressive (80K) — ~$200/mo savings

**Options:**
- `--month` — Scan last 30 days (more data)
- `--all` — Scan all history (takes longer)
- Custom number: `/optimize compaction 100` = compact at 100K tokens

### 📝 Persistent Mode
When enabled, adds file-type-aware writing guidance to AGENTS.md:

| File | Writing Style |
|------|---------------|
| SOUL.md | Evocative, personality-shaping |
| AGENTS.md | Dense instructions, symbols OK |
| USER.md | Key:value facts |
| MEMORY.md | Ultra-dense data |
| memory/*.md | Log format, dated |
| PROJECTS.md | Keep user's structure |

## Safety

- **Auto-backup** — All modified files get `.backup` extension
- **Integrity > Size** — Never sacrifices meaning for smaller tokens
- **Revert anytime** — `/optimize revert` restores all backups
- **No external calls** — All analysis runs locally

## Installation

```
clawhub install token-saver
```

## Version
2.0.0 — Chat compaction, file-aware compression, persistent mode

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demerzels-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
