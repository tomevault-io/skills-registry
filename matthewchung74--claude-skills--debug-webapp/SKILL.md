---
name: debug-webapp
description: Debug the trading webapp frontend using Chrome DevTools MCP. Use when frontend is not behaving as expected, WebSocket connections failing, charts not rendering, or API calls returning errors. Use when this capability is needed.
metadata:
  author: matthewchung74
---

# Debug Webapp Skill

Systematic debugging workflow for the trading-webapp frontend using Chrome DevTools MCP.

---

## Prerequisites

- Chrome browser open with the webapp loaded
- Chrome DevTools MCP server running

---

## Step 1: Get Current State

### Take Page Snapshot
```
mcp__chrome-devtools__take_snapshot
```
This shows the current page structure and identifies interactive elements.

### List Open Pages
```
mcp__chrome-devtools__list_pages
```
Identify which page/tab to debug. Use `select_page` if needed.

---

## Step 2: Check for Errors

### Console Errors
```
mcp__chrome-devtools__list_console_messages
- Filter by types: ["error", "warn"]
```

Look for:
- JavaScript errors
- React errors (component stack traces)
- WebSocket connection failures
- API response errors logged by the app

### Get Details on Specific Error
```
mcp__chrome-devtools__get_console_message
- msgid: <from list above>
```

---

## Step 3: Network Analysis

### List Network Requests
```
mcp__chrome-devtools__list_network_requests
- resourceTypes: ["fetch", "xhr", "websocket"]
```

Look for:
- Failed requests (4xx, 5xx status)
- Slow requests
- WebSocket disconnections
- Missing CORS headers

### Inspect Specific Request
```
mcp__chrome-devtools__get_network_request
- reqid: <from list above>
```

Check:
- Request headers (auth tokens present?)
- Response body (error messages?)
- Timing (slow backend?)

---

## Step 4: Interactive Debugging

### Evaluate JavaScript
```
mcp__chrome-devtools__evaluate_script
- function: "() => { return window.__APP_STATE__ }"
```

Useful checks:
- `() => localStorage.getItem('auth_token')`
- `() => document.querySelectorAll('[data-error]').length`
- `() => window.__WEBSOCKET_STATE__`

### Take Screenshot
```
mcp__chrome-devtools__take_screenshot
- fullPage: true (for full page)
- uid: <element> (for specific component)
```

---

## Step 5: Performance (if needed)

### Start Performance Trace
```
mcp__chrome-devtools__performance_start_trace
- reload: true
- autoStop: true
```

### Analyze Performance Insights
```
mcp__chrome-devtools__performance_analyze_insight
- insightSetId: <from trace>
- insightName: "LCPBreakdown" or "DocumentLatency"
```

---

## Common Issues & Checks

### WebSocket Not Connecting
1. Check console for WebSocket errors
2. Check network for WS connection attempts
3. Verify backend is running: `curl http://localhost:8000/health`
4. Check CORS configuration

### Chart Not Rendering
1. Take screenshot of chart container
2. Check console for TradingView errors
3. Verify data is being received (network tab)
4. Check element visibility in snapshot

### API Calls Failing
1. List network requests filtered to fetch/xhr
2. Check request headers (Authorization present?)
3. Check response status and body
4. Verify backend endpoint exists

### State Not Updating
1. Evaluate script to check React state/context
2. Check for console errors during action
3. Verify WebSocket messages arriving (network tab)

---

## Quick Debug Sequence

For most issues, run this sequence:

1. `list_pages` → identify the page
2. `take_snapshot` → see current UI state
3. `list_console_messages` (errors only) → find JS errors
4. `list_network_requests` (fetch/xhr/websocket) → find API issues
5. `take_screenshot` → visual confirmation

Then dig deeper based on what you find.

---

## Webapp-Specific Locations

| Component | What to Check |
|-----------|---------------|
| Scanner table | WebSocket messages, `/api/candidates` |
| TradingView chart | `tradingview` console logs, symbol data feed |
| Watchlist | `/api/watchlist` endpoint, localStorage |
| Trade panel | `/api/orders`, form validation errors |
| Journal | `/api/journal`, DecisionSnapshot format |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matthewchung74) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
