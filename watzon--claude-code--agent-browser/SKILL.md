---
name: agent-browser
description: MUST USE for any browser automation tasks. Headless browser via agent-browser CLI - web interactions, scraping, testing, screenshots, login flows, and form submission. Uses ref-based element selection (@e1, @e2) from accessibility snapshots. Use when this capability is needed.
metadata:
  author: watzon
---

# agent-browser Skill

## Description

Use this skill for headless browser automation tasks. agent-browser is a CLI designed specifically for AI agents, providing a clean interface to Playwright with ref-based element selection that eliminates the need for complex CSS selectors.

**Use when:**
- Automating web interactions (clicking, typing, form submission)
- Scraping dynamic content that requires JavaScript execution
- Testing web applications
- Logging into websites and performing authenticated actions
- Navigating multi-step web workflows
- Taking screenshots or extracting page content

## Prerequisites

```bash
# Install via npm (runs as daemon, CLI is Rust-based)
npm install -g @anthropic-ai/agent-browser

# Or via npx (no install needed)
npx @anthropic-ai/agent-browser --help
```

## Core Workflow

The fundamental pattern for AI browser automation:

```bash
# 1. Open a page
agent-browser open "https://example.com"

# 2. Get interactive elements with refs
agent-browser snapshot -i

# 3. Interact using refs from snapshot
agent-browser click @e5
agent-browser fill @e12 "search query"

# 4. Re-snapshot after page changes
agent-browser snapshot -i
```

**Key Insight:** Always use refs (`@e1`, `@e2`, etc.) from snapshots rather than CSS selectors. Refs are stable identifiers assigned during snapshot that make element targeting trivial.

## Commands Reference

### Navigation

```bash
# Open URL
agent-browser open "https://example.com"

# Open with custom headers (auth, cookies)
agent-browser open "https://api.example.com" --headers "Authorization: Bearer token123"

# Close browser
agent-browser close
```

### Snapshots (Critical for AI)

```bash
# Full accessibility tree
agent-browser snapshot

# Interactive elements only (RECOMMENDED for AI)
agent-browser snapshot -i

# Compact output (less verbose)
agent-browser snapshot -c

# Limit depth
agent-browser snapshot -d 5

# Filter by CSS selector
agent-browser snapshot -s "#main-content"

# Combine flags
agent-browser snapshot -i -c -d 3

# JSON output for parsing
agent-browser snapshot -i --json
```

### Interactions

```bash
# Click element by ref
agent-browser click @e5

# Click by CSS selector (avoid if possible)
agent-browser click "button.submit"

# Fill input field (clears existing content)
agent-browser fill @e12 "hello@example.com"

# Type text (appends, supports special keys)
agent-browser type @e12 "additional text"

# Press keyboard keys
agent-browser press Enter
agent-browser press Control+a
agent-browser press Tab

# Scroll
agent-browser scroll down
agent-browser scroll up
agent-browser scroll @e5  # Scroll element into view
```

### Data Extraction

```bash
# Get visible text content
agent-browser get text
agent-browser get text @e5  # Specific element

# Get HTML
agent-browser get html
agent-browser get html @e5

# Get input value
agent-browser get value @e12

# Screenshot
agent-browser screenshot output.png
agent-browser screenshot --fullpage output.png
```

### Waiting

```bash
# Wait for element to appear
agent-browser wait @e5
agent-browser wait "div.loaded"

# Wait for navigation
agent-browser wait navigation

# Wait with timeout (ms)
agent-browser wait @e5 --timeout 10000
```

### Session Management

```bash
# Isolated session (separate cookies, storage)
agent-browser --session myproject open "https://example.com"
agent-browser --session myproject snapshot -i
agent-browser --session myproject click @e5

# Default session is used if not specified
```

## Best Practices

### 1. Always Use `-i` Flag for Snapshots

```bash
# Good - only interactive elements
agent-browser snapshot -i

# Avoid - too much noise
agent-browser snapshot
```

### 2. Prefer Refs Over CSS Selectors

```bash
# Good - stable ref from snapshot
agent-browser click @e5

# Avoid - brittle selector
agent-browser click "div.container > ul > li:nth-child(3) > button"
```

### 3. Use `--json` for Parsing

```bash
# Parse snapshot programmatically
agent-browser snapshot -i --json | jq '.elements[] | select(.role == "button")'
```

### 4. Re-snapshot After Page Changes

After any interaction that might change the page (click, submit, navigation), take a fresh snapshot. Refs are invalidated when the DOM changes.

### 5. Use Sessions for Parallel Work

```bash
# Separate sessions for different tasks
agent-browser --session task1 open "https://site1.com"
agent-browser --session task2 open "https://site2.com"
```

## Common Patterns

### Login Flow

```bash
agent-browser open "https://app.example.com/login"
agent-browser snapshot -i
# Output shows: @e3 textbox "Email", @e5 textbox "Password", @e7 button "Sign In"

agent-browser fill @e3 "user@example.com"
agent-browser fill @e5 "password123"
agent-browser click @e7
agent-browser wait navigation
agent-browser snapshot -i  # Verify logged in
```

### Form Submission

```bash
agent-browser open "https://example.com/form"
agent-browser snapshot -i

# Fill multiple fields
agent-browser fill @e2 "John Doe"
agent-browser fill @e4 "john@example.com"
agent-browser fill @e6 "Hello, this is my message."

# Select dropdown (click to open, then click option)
agent-browser click @e8
agent-browser snapshot -i  # Get dropdown options
agent-browser click @e12   # Select option

# Submit
agent-browser click @e15
agent-browser wait navigation
```

### Data Extraction

```bash
agent-browser open "https://example.com/products"
agent-browser snapshot -i --json > products.json

# Or get specific content
agent-browser get text ".product-list"
agent-browser get html "#main-content"
```

### Search and Navigate Results

```bash
agent-browser open "https://example.com"
agent-browser snapshot -i
# @e5 searchbox "Search"

agent-browser fill @e5 "query"
agent-browser press Enter
agent-browser wait navigation
agent-browser snapshot -i
# Shows search results with refs

agent-browser click @e10  # Click first result
```

### Handle Modals/Popups

```bash
# After action triggers modal
agent-browser click @e5
agent-browser snapshot -i  # Modal elements now visible

# Interact with modal
agent-browser fill @e20 "confirmation"
agent-browser click @e22  # Confirm button
```

### Screenshot for Verification

```bash
# After completing workflow
agent-browser screenshot verification.png

# Full page capture
agent-browser screenshot --fullpage full-page.png
```

## Snapshot Output Format

When you run `agent-browser snapshot -i`, output looks like:

```
@e1 link "Home"
@e2 link "Products"
@e3 link "About"
@e4 searchbox "Search..."
@e5 button "Search"
@e6 textbox "Email address"
@e7 button "Subscribe"
```

With `--json`:

```json
{
  "elements": [
    {"ref": "@e1", "role": "link", "name": "Home"},
    {"ref": "@e2", "role": "link", "name": "Products"},
    {"ref": "@e4", "role": "searchbox", "name": "Search..."},
    {"ref": "@e5", "role": "button", "name": "Search"}
  ]
}
```

## Troubleshooting

### Element Not Found

```bash
# Re-snapshot - DOM may have changed
agent-browser snapshot -i

# Check if element is in viewport
agent-browser scroll @e5
agent-browser snapshot -i
```

### Timeout Errors

```bash
# Increase timeout
agent-browser wait @e5 --timeout 30000

# Or wait for specific condition
agent-browser wait navigation
```

### Session Issues

```bash
# Close and restart session
agent-browser close
agent-browser open "https://example.com"
```

### Debugging

```bash
# Take screenshot to see current state
agent-browser screenshot debug.png

# Get full HTML for inspection
agent-browser get html > page.html
```

## Architecture Notes

- **Rust CLI** handles command parsing and communicates with daemon
- **Node.js daemon** manages Playwright browser instances
- Daemon starts automatically on first command
- Sessions provide isolation (cookies, localStorage, separate browser contexts)
- Refs (`@e1`, `@e2`) are assigned per-snapshot and tied to the accessibility tree

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/watzon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
