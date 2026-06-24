---
name: ark-dashboard-test
description: Test the Ark Dashboard UI with Playwright Use when this capability is needed.
metadata:
  author: dwmkerr
---

# Ark Dashboard Test

Test Ark Dashboard with Playwright and capture screenshots.

## When to use

- Testing Ark dashboard UI
- Capturing screenshots for PRs
- Validating dashboard changes

## Prerequisites

- Ark deployed and running
- Playwright MCP server available

## Steps

1. **Port forward the dashboard**
   ```bash
   kubectl port-forward svc/ark-dashboard 3000:3000 -n default &
   ```

2. **Test with Playwright MCP tools**
   - `browser_navigate` - Open http://localhost:3000
   - `browser_snapshot` - Check page state
   - `browser_click` - Interact with elements
   - `browser_take_screenshot` - Capture screenshots

3. **Screenshots location**
   Screenshots save to current directory or `.playwright-mcp/screenshots/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dwmkerr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
