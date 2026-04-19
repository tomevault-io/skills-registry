---
name: canvas
description: | Use when this capability is needed.
metadata:
  author: edmundmiller
---

# Canvas TUI Toolkit

**Start here when using terminal canvases.** This skill covers the overall workflow, canvas types, and IPC communication.

## Example Prompts

Try asking things like:

**Calendar:**
- "Schedule a meeting with the team next week"
- "Find a time when Alice and Bob are both free"

**Document:**
- "Draft an email to the sales team about the new feature"
- "Help me edit this document - let me select what to change"

**Flight:**
- "Find flights from SFO to Denver next Friday"
- "Book me a window seat on the morning flight"

## Overview

Canvas provides interactive terminal displays (TUIs) that can be spawned and controlled via OpenCode tools. Each canvas type supports multiple scenarios for different interaction modes.

## Available Canvas Types

| Canvas | Purpose | Scenarios |
|--------|---------|-----------|
| `calendar` | Display calendars, pick meeting times | `display`, `meeting-picker` |
| `document` | View/edit markdown documents | `display`, `edit`, `email-preview` |
| `flight` | Flight comparison and seat selection | `booking` |

## OpenCode Tools

### Low-Level Tools (Full Control)

| Tool | Purpose |
|------|---------|
| `canvas_spawn` | Spawn a canvas in tmux split pane |
| `canvas_env` | Detect terminal environment (tmux, mouse support) |
| `canvas_update` | Send configuration updates to active canvas |
| `canvas_close` | Close an active canvas |
| `canvas_get_selection` | Get text selection from document canvas |
| `canvas_get_content` | Get full content from document canvas |

### High-Level Tools (Convenience)

| Tool | Purpose |
|------|---------|
| `canvas_display_document` | Display markdown document (read-only) |
| `canvas_edit_document` | Open document for text selection |
| `canvas_pick_meeting_time` | Calendar picker for scheduling |
| `canvas_display_calendar` | Display calendar events (read-only) |

## Quick Start

**Check environment first:**
```
Use canvas_env to verify tmux is available
```

**Spawn a canvas:**
```
Use canvas_spawn with kind="calendar", scenario="meeting-picker"
```

**Or use high-level tools:**
```
Use canvas_display_document with content="# Hello\n\nMarkdown here."
```

## IPC Communication

Interactive canvases communicate via Unix domain sockets.

**Canvas -> Controller:**
```typescript
{ type: "ready", scenario }        // Canvas is ready
{ type: "selected", data }         // User made a selection
{ type: "cancelled", reason? }     // User cancelled
{ type: "error", message }         // Error occurred
```

**Controller -> Canvas:**
```typescript
{ type: "update", config }  // Update canvas configuration
{ type: "close" }           // Request canvas to close
{ type: "ping" }            // Health check
```

## Requirements

- **tmux**: Canvas spawning requires a tmux session
- **Terminal with mouse support**: For click-based interactions
- **Bun**: Runtime for executing canvas commands

## CLI Usage (Standalone)

The canvas CLI is also available for standalone use:

```bash
# Run canvas in current terminal
bun run src/cli.ts show calendar

# Spawn canvas in new tmux split
bun run src/cli.ts spawn calendar --scenario meeting-picker --config '{...}'
```

## Skills Reference

| Skill | Purpose |
|-------|---------|
| `calendar` | Calendar display and meeting picker details |
| `document` | Document rendering and text selection |
| `flight` | Flight comparison and seat map details |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edmundmiller) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
