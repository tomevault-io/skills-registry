---
name: codex-docs-guide
description: | Use when this capability is needed.
metadata:
  author: PsychQuant
---

# Codex Docs Guide

Query OpenAI Codex source code and official documentation for accurate, up-to-date information.

## When to Use

When the user asks about or the conversation involves:
- Codex CLI setup, configuration, or commands
- Codex model selection or switching
- Codex speed, fast mode, or reasoning levels
- Codex AGENTS.md, skills, MCP, rules
- Codex SDK, automation, or non-interactive mode
- Codex app, IDE extension, or cloud
- Any OpenAI Codex product or feature

## Strategy: Source Code First, Docs Second

**Primary source**: [`openai/codex`](https://github.com/openai/codex) GitHub repo — the definitive source for config schemas, valid values, CLI flags, protocol definitions, and SDK types.

**Secondary source**: WebFetch from `https://developers.openai.com/codex/` — for conceptual guides, tutorials, and best practices.

## Execution Steps (IMPORTANT!)

**You MUST query the source — never answer from memory!**

### Step 1: Determine query type and choose method

| Query Type | Method | Example |
|-----------|--------|---------|
| Config valid values, schema | Repo: config.schema.json | "What values does reasoning_effort accept?" |
| CLI flags, options | Repo: main.rs or docs/ | "What flags does `codex` accept?" |
| Model definitions, defaults | Repo: models.json | "What's the default reasoning effort for gpt-5.5?" |
| Protocol types, enums | Repo: protocol src | "What ReasoningEffort variants exist?" |
| SDK types, interfaces | Repo: sdk/ | "What options does exec() accept?" |
| Conceptual guides, tutorials | WebFetch docs site | "How does sandboxing work?" |
| Pricing, enterprise, auth | WebFetch docs site | "How much does Codex cost?" |

### Step 2a: Query GitHub Repo (primary)

**Search for keywords across the repo:**
```bash
gh search code "keyword" --repo openai/codex --limit 20
```

**Read specific key files via raw URL (preferred — avoids base64/size limits):**
```bash
# Config schema (all valid config keys and values) — 78KB
curl -sL https://raw.githubusercontent.com/openai/codex/main/codex-rs/core/config.schema.json

# Model definitions (models, defaults, reasoning efforts) — 251KB, too large for gh api contents
curl -sL https://raw.githubusercontent.com/openai/codex/main/codex-rs/core/models.json

# CLI entry point (flags, arguments)
curl -sL https://raw.githubusercontent.com/openai/codex/main/codex-rs/exec/src/main.rs

# In-repo documentation
gh api repos/openai/codex/contents/docs/ -q '.[].name'
curl -sL https://raw.githubusercontent.com/openai/codex/main/docs/config.md
```

> **IMPORTANT**: Do NOT use `gh api repos/.../contents/FILE -q '.content' | base64 -d` for files > 100KB.
> GitHub API returns empty content for large files. Always use `curl -sL` with raw.githubusercontent.com.

**Key files in `openai/codex` repo:**

| File | Contains |
|------|----------|
| `codex-rs/core/config.schema.json` | Full config schema with all valid values |
| `codex-rs/core/models.json` | Model definitions, defaults, supported efforts |
| `codex-rs/exec/src/main.rs` | CLI entry point, flags, arguments |
| `codex-rs/protocol/src/config_types.rs` | Rust config types and enums |
| `codex-rs/protocol/src/openai_models.rs` | ReasoningEffort enum, model types |
| `sdk/typescript/src/threadOptions.ts` | TypeScript SDK types |
| `sdk/typescript/src/exec.ts` | SDK exec options, CLI arg mapping |
| `sdk/python/src/codex_app_server/` | Python SDK types |
| `codex-rs/docs/` | Internal protocol and interface docs |
| `docs/` | User-facing documentation (markdown) |

### Step 2b: Query Docs Site (secondary)

Prepend `https://developers.openai.com` to paths:

| Topic | URL Path |
|-------|----------|
| Codex overview | /codex/ |
| Quickstart | /codex/quickstart/ |
| CLI overview | /codex/cli/ |
| CLI features | /codex/cli/features/ |
| CLI reference | /codex/cli/reference |
| Slash commands | /codex/cli/slash-commands/ |
| Config basics | /codex/config/basics/ |
| Speed & fast mode | /codex/speed/ |
| Models | /codex/models/ |
| AGENTS.md | /codex/agents-md/ |
| MCP setup | /codex/mcp/ |
| Skills | /codex/skills/ |
| SDK | /codex/sdk/ |
| Non-interactive | /codex/non-interactive/ |
| Security | /codex/security/ |
| Best practices | /codex/learn/best-practices/ |

```
WebFetch("https://developers.openai.com/codex/cli/reference", "Extract documentation about...")
```

### Step 3: Parse and respond

Extract relevant information and answer the user directly.

## Quick Reference

### Installation
```bash
npm install -g @openai/codex    # npm
brew install codex              # Homebrew
```

### Config file
`~/.codex/config.toml` — primary config (TOML format, supports profiles)

### Key CLI flags
| Flag | Purpose |
|------|---------|
| `-m, --model` | Override model (e.g. `gpt-5.5`) |
| `-a, --ask-for-approval` | `untrusted` / `on-request` / `never` |
| `-s, --sandbox` | `read-only` / `workspace-write` / `danger-full-access` |
| `-i, --image` | Attach image files |
| `-p, --profile` | Load named config profile |
| `--full-auto` | Low-friction auto mode |
| `--search` | Enable web search |
| `--oss` | Use local OSS model (Ollama) |

### Reasoning Effort Levels
`none` | `minimal` | `low` | `medium` | `high` | `xhigh`

### Key paths
| Path | Purpose |
|------|---------|
| `~/.codex/config.toml` | User config |
| `~/.codex/AGENTS.md` | Global agent instructions |
| `AGENTS.md` | Repo-level instructions |
| `.agents/skills/` | Repo skills |
| `$HOME/.agents/skills` | Global skills |

## Fallback

If topic is not covered above:
1. `gh search code "topic" --repo openai/codex`
2. WebSearch for `site:developers.openai.com/codex <topic>`
3. WebFetch `https://developers.openai.com/codex/` for the main index

## Important Reminders

- **Source code is authoritative** for valid values, schemas, and types — prefer repo over docs
- **Docs site is authoritative** for guides, tutorials, and conceptual explanations
- Config is TOML (`config.toml`), not JSON — different from Claude Code
- Some doc URL paths may 404 — if so, try the parent path or use WebSearch

---
> Source: [PsychQuant/psychquant-claude-plugins](https://github.com/PsychQuant/psychquant-claude-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
