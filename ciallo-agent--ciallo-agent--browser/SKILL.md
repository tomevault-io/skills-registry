---
name: browser
description: Browser automation using @sdjz/pup. AXTree scanning, stealth mode, DevTools access. Designed for AI agents. Use when this capability is needed.
metadata:
  author: ciallo-agent
---

# Browser Skill (pup)

## Install
```bash
npm install -g @sdjz/pup
```

## Quick Start
```powershell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

pup goto https://example.com     # Open page
pup scan --no-empty              # Scan elements, get IDs
pup click 5                      # Click element by ID
pup type 3 "hello" --enter       # Type and press Enter
```

## Core Commands

### Navigation
```powershell
pup goto <url>                   # Open URL
pup back / forward               # History
pup reload                       # Reload
pup scroll <up|down|top|bottom>  # Scroll
pup wait <ms>                    # Wait
```

### Element Interaction
```powershell
pup scan --no-empty --limit 30   # Scan viewport elements
pup find <text>                  # Find by text
pup click <id>                   # Click element
pup click <id> --js              # JS click (for modals)
pup type <id> <text> --enter     # Type + Enter
pup hover <id>                   # Mouse hover
pup select <id> <option>         # Select dropdown
```

### Tab Management
```powershell
pup tabs                         # List tabs
pup tab <id>                     # Switch tab
pup newtab [url]                 # New tab
pup close                        # Close current
```

### DevTools
```powershell
pup network --capture            # Enable XHR capture
pup cookies                      # View cookies
pup storage                      # View localStorage
pup exec <js>                    # Execute JavaScript
pup screenshot [file]            # Take screenshot
```

### Performance
```powershell
pup perf                         # Quick Web Vitals
pup perf-network                 # Network waterfall
pup perf-memory                  # Memory analysis
```

## Agent Workflow
```
1. pup goto https://example.com
2. pup scan --no-empty           # Get element IDs
3. LLM decides: click element 5
4. pup click 5
5. pup scan --no-empty           # Check result
6. ...loop
```

## JSON Mode (for automation)
```powershell
pup scan --json                  # JSON output
```

## Notes
- Original: shot-scraper (2025-12-17, 10 coins)
- Upgraded to: @sdjz/pup (2026-01-01, free - shuakami's gift!)
- Features: AXTree scanning, Bezier mouse movement, stealth mode, plugin architecture

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ciallo-agent) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
