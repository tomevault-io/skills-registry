---
name: clojure-mcp
description: Use when working with ClojureMCP, the MCP server that connects AI assistants to Clojure projects through nREPL and Clojure-aware tools. Activate when the user asks about installing clojure-mcp, `clojure -Tmcp start`, `:cli-assist`, Claude Code/Codex/Gemini MCP setup, Claude Desktop setup, nREPL ports, `:start-nrepl-cmd`, `.clojure-mcp/config.edn`, allowed directories, tool enable/disable lists, Clojure-aware editing tools, custom MCP tools/prompts/resources, model/tool configuration, project summaries, scratch pad, or troubleshooting ClojureMCP connections.
metadata:
  author: stoating
---

# ClojureMCP

ClojureMCP is an MCP server for Clojure development. It connects an LLM client to a project through nREPL and exposes Clojure-aware tools for REPL evaluation, file reading/search, structural editing, prompts, resources, and optional agent tools.

## Quick Decision: What do you need?

| Task | Go to |
|------|-------|
| Install ClojureMCP and add it to Claude Code, Codex, Gemini, or Claude Desktop | [setup.md](setup.md) |
| Configure nREPL ports, auto-start, project directory, CLI args, and desktop launching | [nrepl.md](nrepl.md) |
| Understand available tools and when to use `:cli-assist` | [tools.md](tools.md) |
| Configure `.clojure-mcp/config.edn`, allowed directories, tool filters, scratch pad, bash mode | [configuration.md](configuration.md) |
| Create or customize tools, prompts, resources, and custom MCP servers | [customization.md](customization.md) |
| Use project summaries, chat resume, REPL-driven workflows, and multiple REPLs | [workflows.md](workflows.md) |
| Fix connection, path, nREPL, tool, env var, or Claude Desktop issues | [troubleshooting.md](troubleshooting.md) |

## Core Mental Model

ClojureMCP sits between an MCP client and a Clojure project. The MCP process serves tools over JSON-RPC; most useful Clojure behavior comes from a live nREPL connected to the target project.

For CLI assistants, prefer keeping native file editing and shell tools and add ClojureMCP mainly for REPL integration and Clojure-aware fallback tools:

```bash
clojure -Tmcp start :config-profile :cli-assist
```

For desktop clients, start an nREPL in the project and configure the desktop app to launch ClojureMCP against that nREPL. Desktop clients usually need a fuller ClojureMCP toolchain because they do not provide strong local coding tools themselves.

---
> Source: [stoating/clojure-clojure-mcp-skill](https://github.com/stoating/clojure-clojure-mcp-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
