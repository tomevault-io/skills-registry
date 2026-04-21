---
name: gemini-cli-guide
description: Complete Gemini CLI usage guide. Use when users ask about Gemini CLI commands, flags, options, configuration, or usage patterns. Triggers on questions like "how to use gemini command", "gemini cli flags", "how to configure gemini", "gemini slash commands", "gemini authentication", "GEMINI.md file", or requests for Gemini CLI examples. Use when this capability is needed.
metadata:
  author: momochenisme
---

# Gemini CLI Reference

Quick reference for Gemini CLI commands and configuration.

## Installation

| Method | Command |
|--------|---------|
| NPX (no install) | `npx @google/gemini-cli` |
| NPM | `npm install -g @google/gemini-cli` |
| Homebrew | `brew install gemini-cli` |
| MacPorts | `sudo port install gemini-cli` |

## Authentication

| Method | Setup |
|--------|-------|
| Google OAuth | Run `gemini` and select "Login with Google" |
| API Key | Set `GEMINI_API_KEY` env var (get from aistudio.google.com/apikey) |
| Vertex AI | Set `GOOGLE_API_KEY` + `GOOGLE_GENAI_USE_VERTEXAI=true` |

## Basic Commands

| Command | Description |
|---------|-------------|
| `gemini` | Start interactive REPL |
| `gemini "query"` | Start REPL with initial prompt |
| `gemini -p "query"` | One-shot query (pipe mode) |

## Common Flags

| Flag | Description |
|------|-------------|
| `-p, --prompt <text>` | One-shot query mode (pipe mode) |
| `-i, --prompt-interactive` | Interactive prompt mode |
| `--input <text>` | Input for the prompt |
| `-m, --model` | Specify model |
| `-s, --sandbox` | Enable sandbox mode |
| `--approval-mode` | Set approval mode (default/auto_edit/plan/yolo) |
| `--output-format` | Output format (text/json/stream-json) |
| `-d, --debug` | Enable debug mode |
| `-r, --resume` | Resume previous conversation |
| `--list-sessions` | List available sessions |
| `--delete-session <id>` | Delete a specific session |
| `--allowed-tools <list>` | Specify tools that bypass confirmation |
| `-e, --extensions <names>` | Specify extensions to use |
| `-l, --list-extensions` | List available extensions |
| `--allowed-mcp-server-names` | Configure MCP servers |
| `--screen-reader` | Screen reader optimization |
| `--color` / `--no-color` | Control colored output |

## Slash Commands

| Category | Commands |
|----------|----------|
| Session | `/chat`, `/clear`, `/resume`, `/rewind`, `/save`, `/quit` |
| Config | `/settings`, `/auth`, `/theme`, `/vim`, `/model` |
| Tools | `/tools`, `/mcp`, `/hooks`, `/skills`, `/extensions` |
| Memory | `/memory`, `/compress` |
| Context | `/directory`, `/restore` |
| Workflow | `/bug`, `/copy`, `/editor`, `/init`, `/setup-github`, `/export` |
| Info | `/help`, `/about`, `/docs`, `/stats`, `/privacy`, `/introspect`, `/version` |
| Shell | `/shells` (or `/bashes`) |
| IDE | `/ide`, `/terminal-setup`, `/policies` |

## Special Syntax

| Syntax | Description |
|--------|-------------|
| `@path` | Include file/directory in context |
| `!command` | Execute shell command |

## Configuration Files

| Location | Scope |
|----------|-------|
| `~/.gemini/settings.json` | Global settings |
| `.gemini/settings.json` | Project settings |
| `GEMINI.md` | Project instructions |

## Detailed References

- [commands.md](references/commands.md) - All slash, @ and ! commands
- [flags.md](references/flags.md) - All CLI flags by category
- [configuration.md](references/configuration.md) - Settings, env vars, MCP config
- [examples.md](references/examples.md) - Common usage examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/momochenisme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
