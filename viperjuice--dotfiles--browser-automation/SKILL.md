---
name: browser-automation
description: Guide for browser automation on this machine. Covers when to use Playwright plugin vs claude-in-chrome vs CDP, port/service architecture (Xvfb :99, CDP 9222, VNC 5900/6080), authenticated sessions via storageState, and common pitfalls. Use when performing any browser interaction, taking screenshots, testing web UIs, or debugging extension behavior. Use when this capability is needed.
metadata:
  author: viperjuice
---

# Browser Automation

## Decision Tree

```
Need browser automation?
├─ Extension context needed? (chrome.storage, service workers, extension popups)
│   ├─ Quick/interactive → claude-in-chrome (EZBidPro only, single-session)
│   └─ Scripted/CDP-level → Playwright CDP to :9222 + manual target attachment
│
├─ Need to see the real headed Chrome state? (inspect what user sees on :99)
│   └─ Yes → Playwright CDP connection to 127.0.0.1:9222
│
└─ Everything else (default)
    └─ Playwright plugin (mcp__plugin_playwright_playwright__*)
```

## Playwright Plugin (Default)

Tools: `mcp__plugin_playwright_playwright__browser_*`

Launches its own isolated Chromium — no contention with other sessions, no port conflicts. Use for:
- Screenshots and visual verification
- Form filling, clicking, navigation
- Testing local web apps
- Any browser task that doesn't need the real Chrome instance

Key tools:
- `browser_navigate` — go to URL
- `browser_snapshot` — accessibility tree (better than screenshot for actions)
- `browser_take_screenshot` — visual capture
- `browser_click` / `browser_type` / `browser_fill_form` — interaction
- `browser_evaluate` — run JS on page
- `browser_console_messages` — read console output

### Authenticated Sessions

Save login state:
```javascript
// After logging in, save state
await page.context().storageState({ path: '/tmp/auth-state.json' });
```

Restore in new session:
```javascript
const context = await browser.newContext({ storageState: '/tmp/auth-state.json' });
```

## Playwright CDP Connection

Connect to the real headed Chrome running on display :99:

```python
from playwright.sync_api import sync_playwright
with sync_playwright() as p:
    browser = p.chromium.connect_over_cdp('http://127.0.0.1:9222')
    page = browser.contexts[0].pages[0]  # existing tab
    page.screenshot(path='/tmp/current-state.png')
```

Use when you need to inspect or interact with the actual browser the user sees via VNC. This is read/write access to the real Chrome instance.

## claude-in-chrome

Tools: `mcp__claude-in-chrome__*`

**Restricted to EZBidPro project tree** — disabled globally via `"claude-in-chrome": false` in `enabledPlugins` (`~/.claude/settings.json`) to prevent MCP contention (single WebSocket, multiple sessions = 7+ minute hangs). Enabled only in EZBidPro via project-level `enabledPlugins`.

Use only when you need access to Chrome extension internals:
- Extension popup/sidebar DOM
- `chrome.storage` API
- Service worker contexts
- Extension-injected content scripts

**Single-session limitation**: Only one Claude Code session can use claude-in-chrome at a time. If tools hang with no response, another session likely holds the WebSocket.

## Port Deconfliction

- **CDP 9222** — headed Chrome on display :99 (system service)
- **VNC 5900** — raw VNC to display :99
- **NoVNC 6080** — web VNC viewer at `http://127.0.0.1:6080`
- **Playwright** — launches on random ports, fully isolated

Never configure Playwright to use port 9222 unless intentionally connecting to the headed Chrome via CDP.

For full infrastructure details, see [references/INFRASTRUCTURE.md](references/INFRASTRUCTURE.md).

## CDP vs claude-in-chrome for Extension Work

CDP **can** access extension targets when Chrome has a persistent profile and extensions loaded. The headed Chrome on `:9222` already meets these requirements.

### Inspecting extensions via CDP

1. Enumerate targets: `Target.getTargets` returns all browser targets including extensions
2. Find extension targets: look for `type: "service_worker"` or `"background_page"` with `url` starting with `chrome-extension://`
3. Create a `CDPSession` to the target and enable domains: `Runtime`, `Log`, `Network`, `Storage`
4. Extension popup pages are regular `chrome-extension://<id>/popup.html` URLs — open directly in a tab and use Playwright selectors

### Caveats

- **MV3 service workers unload frequently** — must listen for `Target.targetCreated` / `Target.targetDestroyed` to reattach
- **`chrome.storage` access** requires evaluating JS in the extension context via `Runtime.evaluate` on the attached target
- **Content scripts** run in isolated worlds — use `Runtime.evaluate` with the correct `executionContextId`

### When to use claude-in-chrome instead

claude-in-chrome is a pre-built convenience layer with tools already wired to the extension. Its value is zero-setup access to extension internals without manual target wiring. The tradeoff is the single-session WebSocket limitation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/viperjuice) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
