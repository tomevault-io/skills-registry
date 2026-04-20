---
name: agent-browser
description: Automates browser interactions for web testing, form filling, screenshots, and data extraction. Use when the user needs to navigate websites, interact with web pages, fill forms, take screenshots, test web applications, or extract information from web pages.
metadata:
  author: hajimism
---

# Browser Automation with agent-browser

## Quick start

```bash
bunx agent-browser open <url>        # Navigate to page
bunx agent-browser snapshot -i       # Get interactive elements with refs
bunx agent-browser click @e1         # Click element by ref
bunx agent-browser fill @e2 "text"   # Fill input by ref
bunx agent-browser close             # Close browser
```

## Core workflow

1. Navigate: `bunx agent-browser open <url>`
2. Snapshot: `bunx agent-browser snapshot -i` (returns elements with refs like `@e1`, `@e2`)
3. Interact using refs from the snapshot
4. Re-snapshot after navigation or significant DOM changes

## Commands

### Navigation

```bash
bunx agent-browser open <url>      # Navigate to URL
bunx agent-browser back            # Go back
bunx agent-browser forward         # Go forward
bunx agent-browser reload          # Reload page
bunx agent-browser close           # Close browser
```

### Snapshot (page analysis)

```bash
bunx agent-browser snapshot        # Full accessibility tree
bunx agent-browser snapshot -i     # Interactive elements only (recommended)
bunx agent-browser snapshot -c     # Compact output
bunx agent-browser snapshot -d 3   # Limit depth to 3
```

### Interactions (use @refs from snapshot)

```bash
bunx agent-browser click @e1           # Click
bunx agent-browser dblclick @e1        # Double-click
bunx agent-browser fill @e2 "text"     # Clear and type
bunx agent-browser type @e2 "text"     # Type without clearing
bunx agent-browser press Enter         # Press key
bunx agent-browser press Control+a     # Key combination
bunx agent-browser hover @e1           # Hover
bunx agent-browser check @e1           # Check checkbox
bunx agent-browser uncheck @e1         # Uncheck checkbox
bunx agent-browser select @e1 "value"  # Select dropdown
bunx agent-browser scroll down 500     # Scroll page
bunx agent-browser scrollintoview @e1  # Scroll element into view
```

### Get information

```bash
bunx agent-browser get text @e1        # Get element text
bunx agent-browser get value @e1       # Get input value
bunx agent-browser get title           # Get page title
bunx agent-browser get url             # Get current URL
```

### Screenshots

```bash
bunx agent-browser screenshot          # Screenshot to stdout
bunx agent-browser screenshot path.png # Save to file
bunx agent-browser screenshot --full   # Full page
```

### Wait

```bash
bunx agent-browser wait @e1                     # Wait for element
bunx agent-browser wait 2000                    # Wait milliseconds
bunx agent-browser wait --text "Success"        # Wait for text
bunx agent-browser wait --load networkidle      # Wait for network idle
```

### Semantic locators (alternative to refs)

```bash
bunx agent-browser find role button click --name "Submit"
bunx agent-browser find text "Sign In" click
bunx agent-browser find label "Email" fill "user@example.com"
```

## Example: Form submission

```bash
bunx agent-browser open https://example.com/form
bunx agent-browser snapshot -i
# Output shows: textbox "Email" [ref=e1], textbox "Password" [ref=e2], button "Submit" [ref=e3]

bunx agent-browser fill @e1 "user@example.com"
bunx agent-browser fill @e2 "password123"
bunx agent-browser click @e3
bunx agent-browser wait --load networkidle
bunx agent-browser snapshot -i  # Check result
```

## Example: Authentication with saved state

```bash
# Login once
bunx agent-browser open https://app.example.com/login
bunx agent-browser snapshot -i
bunx agent-browser fill @e1 "username"
bunx agent-browser fill @e2 "password"
bunx agent-browser click @e3
bunx agent-browser wait --url "**/dashboard"
bunx agent-browser state save auth.json

# Later sessions: load saved state
bunx agent-browser state load auth.json
bunx agent-browser open https://app.example.com/dashboard
```

## Sessions (parallel browsers)

```bash
bunx agent-browser --session test1 open site-a.com
bunx agent-browser --session test2 open site-b.com
bunx agent-browser session list
```

## JSON output (for parsing)

Add `--json` for machine-readable output:

```bash
bunx agent-browser snapshot -i --json
bunx agent-browser get text @e1 --json
```

## Debugging

```bash
bunx agent-browser open example.com --headed  # Show browser window
bunx agent-browser console                    # View console messages
bunx agent-browser errors                     # View page errors
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hajimism) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
