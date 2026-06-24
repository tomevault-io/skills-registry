---
name: presentfast-setup
description: Register the PresentFast MCP server with Claude Code. Detects whether the presentfast MCP is already wired and, if not, prints / offers to run the claude mcp add command. Use when this capability is needed.
metadata:
  author: khush012
---

# PresentFast MCP Setup

When invoked, run this two-step check:

1. **Detect existing MCP** — run `claude mcp list 2>&1 | grep -i presentfast` via Bash. If the output contains a line starting with `presentfast:`, report "✓ presentfast MCP already registered" and exit.

2. **Register the MCP** — if not registered, print the exact command for the user to run:

   ```bash
   claude mcp add presentfast -e PF_API_URL=https://www.presentfast.com -- npx -y @presentfast/mcp-server@latest
   ```

   Offer to run it via Bash (with the user's permission). After it runs, verify with `claude mcp list` again and report success.

After the MCP is registered, two tools become callable:
- `mcp__presentfast__create_visual_question`
- `mcp__presentfast__wait_for_selection`

These are what the `/visual` skill (and the PostToolUse trigger hook) rely on.

## Why this is a separate command

The Claude Code plugin install does not (and cannot) auto-register MCP servers — MCP config lives in the user's `~/.claude.json` / `.mcp.json` and is intentionally user-controlled. This skill closes the gap with one explicit invocation.

---
> Source: [khush012/presentfast-visual-questions-plugin](https://github.com/khush012/presentfast-visual-questions-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
