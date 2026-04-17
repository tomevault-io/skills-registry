---
name: browsh
description: Use when working with a modern text-based browser. Renders web pages in the terminal using headless Firefox.
metadata:
  author: openclaw
---

# Browsh

A fully-modern text-based browser. It renders stories and videos, filters ads, and saves bandwidth.

## Prerequisites
- `browsh` binary must be in PATH.
- `firefox` binary must be in PATH (Browsh uses it as a headless backend).

**Local Setup (if installed in `~/apps`):**
Ensure your PATH includes the installation directories:
```bash
export PATH=$HOME/apps:$HOME/apps/firefox:$PATH
```

## Usage

Start Browsh:
```bash
browsh
```

Open a specific URL:
```bash
browsh --startup-url https://google.com
```

**Note:** Browsh is a TUI application. Run it inside a PTY session (e.g., using `tmux` or the `process` tool with `pty=true`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw)
> Context snippets also available to append to your CLAUDE.md, GEMINI.md, and copilot-instructions.md — [download at TomeVault](https://tomevault.io/claim/openclaw)
<!-- tomevault:4.0:skill_md:2026-04-08 -->
