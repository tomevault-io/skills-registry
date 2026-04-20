---
name: openwork-chrome-mcp-testing
description: | Use when this capability is needed.
metadata:
  author: different-ai
---

## Quick Usage (Already Configured)

### 1) Start headless + web UI

Start the OpenWork dev stack via Docker and open the UI:
- Use `skills/openwork-docker-chrome-mcp/SKILL.md` step 1.
- Confirm the Web UI loads and a session/chat view is accessible.

### 2) Test the feature in the UI

- Navigate to the relevant screen.
- Perform the feature flow end-to-end.
- If the change touches remote behavior, verify the remote effect is visible in the UI.

### 3) Capture evidence

- Take a Chrome MCP snapshot.
- Pull console logs with Chrome MCP.
- Attach logs if something fails:
  - capture Chrome console output via Chrome MCP
  - optionally capture Docker logs for the dev stack

## Required Gate (Non-Negotiable)

- Any feature that changes remote behavior MUST be validated through Chrome MCP.
- The feature is NOT done until the UI action succeeds in the browser via Chrome MCP.
- If Chrome MCP validation is skipped, the change is considered incomplete and must not be merged.

## Common Gotchas

- If OpenCode Engine stays disconnected, ensure the OpenWork URL is clean and the client token is correct.
- If you see CORS errors, check OpenWork server CORS headers for custom OpenCode headers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/different-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
