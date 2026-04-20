---
name: browser-control
description: Navigate browser tabs, open URLs, control Chrome. Use when user says "open", "show me", "navigate to", "go to", or mentions viewing pages, tickets, or dashboards in the browser. Use when this capability is needed.
metadata:
  author: erezgit
---

# Browser Control

Control the browser without opening new tabs. Navigate within the current tab.

## Navigate to URL (Same Tab)

Use AppleScript to update the current Chrome tab:

```bash
osascript -e 'tell application "Google Chrome" to set URL of active tab of front window to "URL_HERE"'
```

## Common URLs

| Request | URL |
|---------|-----|
| Tickets dashboard | http://localhost:3200 |
| Specific ticket | http://localhost:3200/tickets/{id} |
| Priority view | http://localhost:3200 (then click Priority List) |

## Examples

**User says:** "Open ticket 193"
```bash
osascript -e 'tell application "Google Chrome" to set URL of active tab of front window to "http://localhost:3200/tickets/193"'
```

**User says:** "Show me the dashboard"
```bash
osascript -e 'tell application "Google Chrome" to set URL of active tab of front window to "http://localhost:3200"'
```

## Notes

- Always use same-tab navigation (AppleScript) instead of `open` command
- `open` creates new tabs; AppleScript updates existing tab
- Works with Google Chrome on macOS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erezgit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
