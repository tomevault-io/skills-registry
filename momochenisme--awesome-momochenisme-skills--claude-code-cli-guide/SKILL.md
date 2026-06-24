---
name: claude-code-cli-guide
description: Complete Claude Code CLI usage guide. Use when users ask about CLI commands, flags, options, or usage patterns. Triggers on questions like "how to use claude command", "what flags are available", "how to run claude in pipe mode", "CLI options", or requests for command examples. Use when this capability is needed.
metadata:
  author: momochenisme
---

# CLI Reference

Quick reference for Claude Code CLI commands and flags.

## Basic Commands

| Command | Description |
|---------|-------------|
| `claude` | Start interactive REPL |
| `claude "query"` | Start REPL with initial prompt |
| `claude -p "query"` | One-shot query (pipe/SDK mode) |
| `claude -c` | Continue most recent conversation |
| `claude -r <id>` | Resume specific session |
| `claude config` | Configuration management |
| `claude mcp` | MCP server configuration |
| `claude update` | Update to latest version |

## Common Flags

| Flag | Description |
|------|-------------|
| `-p, --print` | One-shot query mode (pipe/SDK mode) |
| `-c, --continue` | Continue most recent conversation |
| `-r, --resume <id\|name>` | Resume session by ID or name |
| `--model <name>` | Specify model (opus/sonnet/haiku) |
| `--fallback-model <name>` | Fallback model when primary unavailable |
| `--output-format <fmt>` | Output format (text/json/stream-json) |
| `--max-turns <n>` | Limit agent turns |
| `--max-budget-usd <n>` | Maximum budget in USD |
| `--add-dir <path>` | Add working directory |
| `--system-prompt <p>` | Override system prompt |
| `--append-system-prompt <p>` | Append to system prompt |
| `--system-prompt-file <path>` | Load system prompt from file |
| `--append-system-prompt-file <path>` | Append prompt from file |
| `--allowedTools <patterns>` | Restrict tools (supports glob patterns) |
| `--disallowedTools <patterns>` | Disable tools (supports glob patterns) |
| `--tools <list>` | Limit available tools |
| `--permission-mode <mode>` | Permission mode (default/acceptEdits/plan/dontAsk/bypassPermissions) |
| `--dangerously-skip-permissions` | Skip permission prompts (dangerous) |
| `--agent <name>` | Specify agent to use |
| `--agents <json>` | Define custom subagents (JSON format) |
| `--chrome` / `--no-chrome` | Chrome browser integration |
| `--remote` | Create web session |
| `--teleport <id>` | Resume web session |
| `--from-pr <url>` | Resume session from GitHub PR |
| `--fork-session` | Create new session ID |
| `--session-id <id>` | Specify session ID |
| `--plugin-dir <path>` | Load plugins from directory |
| `--verbose` | Verbose logging |
| `--debug` | Debug mode |

## Detailed References

- [commands.md](references/commands.md) - All commands explained
- [flags.md](references/flags.md) - All flags by category
- [examples.md](references/examples.md) - Common usage examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/momochenisme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
