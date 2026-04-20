---
name: playwright-mcp-cli
description: Automates browser interactions for web testing, form filling, screenshots, and data extraction. Use when the user needs to navigate websites, interact with web pages, fill forms, take screenshots, test web applications, or extract information from web pages. Supports advanced features like multi-tab workflows, network monitoring, tracing, video recording, and test generation.
metadata:
  author: ayatoashihara
---

# Browser Automation with Playwright MCP CLI

Automates browser interactions using the `playwright-cli` command-line tool.

## Core Workflow

1. **Navigate**: `playwright-cli open https://example.com`
2. **Snapshot**: `playwright-cli snapshot` to get element refs (`e1`, `e2`, etc.)
3. **Interact**: Use refs to click, fill, type, etc.
4. **Re-snapshot**: After significant changes to get updated refs

## Quick Start

```bash
# Open a page
playwright-cli open https://example.com

# Get element references
playwright-cli snapshot

# Interact with elements (use refs from snapshot)
playwright-cli click e15
playwright-cli fill e5 "user@example.com"
playwright-cli type "search query"
playwright-cli press Enter

# Capture results
playwright-cli screenshot
```

## Common Tasks

### Form Submission

```bash
playwright-cli open https://example.com/form
playwright-cli snapshot
playwright-cli fill e1 "user@example.com"
playwright-cli fill e2 "password123"
playwright-cli click e3
playwright-cli snapshot
```

### Multi-Tab Workflow

```bash
playwright-cli open https://example.com
playwright-cli tab-new https://example.com/other
playwright-cli tab-list
playwright-cli tab-select 0
playwright-cli snapshot
```

### Screenshots and PDF

```bash
playwright-cli screenshot              # Full page
playwright-cli screenshot e5           # Specific element
playwright-cli pdf                     # Save as PDF
```

### Debugging

```bash
playwright-cli console                 # Console messages
playwright-cli console warning         # Filter by level
playwright-cli network                 # Network requests
```

## Core Commands

### Navigation & Interaction

- `open [url]` - Open URL
- `close` - Close page
- `click <ref>` - Click element
- `fill <ref> <text>` - Fill input
- `type <text>` - Type in focused element
- `press <key>` - Press keyboard key
- `snapshot` - Get element refs

### Tabs

- `tab-list` - List all tabs
- `tab-new [url]` - Create new tab
- `tab-close [index]` - Close tab
- `tab-select <index>` - Switch tab

### DevTools

- `console [level]` - Console logs
- `network` - Network requests
- `tracing-start/stop` - Record trace
- `video-start/stop` - Record video

### Sessions

- `--session=name` - Named session
- `session-list` - List sessions
- `session-stop [name]` - Stop session
- `session-delete [name]` - Delete session

## Advanced Features

For detailed guides on advanced topics, see:

- **Request mocking**: [references/request-mocking.md](references/request-mocking.md)
- **Test generation**: [references/test-generation.md](references/test-generation.md)
- **Tracing**: [references/tracing.md](references/tracing.md)
- **Video recording**: [references/video-recording.md](references/video-recording.md)
- **Storage state**: [references/storage-state.md](references/storage-state.md)
- **Running Playwright code**: [references/running-code.md](references/running-code.md)
- **Session management**: [references/session-management.md](references/session-management.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayatoashihara) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
