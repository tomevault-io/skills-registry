---
name: chrome-devtools-mcp
description: >- Use when this capability is needed.
metadata:
  author: michael0520
---

Automate browser interactions, capture screenshots, inspect the DOM, and verify UI behavior — all from Claude Code through the Chrome DevTools MCP server.

## Prerequisites

```bash
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

Headless or isolated sessions:

```bash
# No UI
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest --headless

# Clean browser state
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest --isolated
```

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Tools | All 26 MCP tools organized by category | [tools](references/tools.md) |
| Patterns | Common testing workflows (forms, responsive, perf, network) | [patterns](references/patterns.md) |
| Examples | Complete worked examples (login form, responsive testing) | [examples](references/examples.md) |

## Quick Reference

```
# Open dev server
mcp__chrome-devtools__new_page → http://localhost:{PORT}

# Get page structure (always do this first)
mcp__chrome-devtools__take_snapshot

# Interact (use UID from snapshot)
mcp__chrome-devtools__click → uid: "abc123"
mcp__chrome-devtools__fill → uid: "input1", value: "test"

# Verify
mcp__chrome-devtools__take_screenshot
mcp__chrome-devtools__list_console_messages → types: ["error", "warn"]
```

## Tips

1. **Always `take_snapshot` first** — you need element UIDs before interacting.
2. **Prefer snapshot over screenshot** — faster and provides actionable data.
3. **Check console after interactions** — catch runtime errors early.
4. **Use `wait_for` after navigation** — ensure the page is loaded before interacting.
5. **Filter network requests** — use `resourceTypes` to focus on API calls.
6. **Re-snapshot after interactions** — UIDs may change after DOM updates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michael0520) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
