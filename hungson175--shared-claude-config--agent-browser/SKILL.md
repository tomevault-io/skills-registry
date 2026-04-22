---
name: agent-browser
description: Headless browser automation CLI for AI agents. Use this skill when you need to automate web interactions, scrape web pages, test web applications, or perform any browser-based tasks. Triggers include "browse", "automate browser", "scrape website", "test web app", "fill form", "take screenshot", or any web automation request. Provides accessibility-first element targeting with semantic locators. Use when this capability is needed.
metadata:
  author: hungson175
---

# Agent-Browser: Browser Automation for AI Agents

Automate web browser interactions using a fast CLI designed specifically for AI agents. Uses accessibility trees and semantic locators for reliable element targeting.

## Installation

```bash
npm install -g agent-browser
agent-browser install  # Download Chromium
```

For Linux, if you encounter library errors:
```bash
agent-browser install --with-deps
```

## Core Concepts

### 1. Element References (`@e` System)
Elements receive `@e` references from `snapshot` for reliable targeting:
```bash
agent-browser snapshot        # Get accessibility tree
agent-browser click @e123     # Click element with reference @e123
```

### 2. Sessions (Isolated Browser Instances)
Run multiple isolated browser contexts:
```bash
agent-browser --session user1 open example.com
agent-browser --session user2 open example.com
```

## Essential Workflow

### Step 1: Open Page and Get Snapshot
```bash
# Navigate to page
agent-browser open "https://example.com"

# Get accessibility tree with element references
agent-browser snapshot
```

**Snapshot options** (reduce output):
- `-i` - Interactive elements only
- `-c` - Compact mode
- `-d 3` - Limit depth to 3
- `-s "#main"` - Scope to specific selector

### Step 2: Interact with Elements

**Using semantic locators** (preferred):
```bash
# Find and click by role
agent-browser find role button click --name "Submit"

# Find and type by label
agent-browser find label "Email" fill "user@example.com"

# Find by text content
agent-browser find text "Sign In" click
```

**Using selectors or @references**:
```bash
agent-browser click "#submit-btn"
agent-browser click @e42
agent-browser fill "input[name='email']" "user@example.com"
agent-browser type "#password" "secret123"
```

### Step 3: Extract Information
```bash
# Get text content
agent-browser get text ".result"

# Get HTML
agent-browser get html "#content"

# Take screenshot
agent-browser screenshot output.png
agent-browser screenshot --full  # Full page
```

## Common Commands Reference

### Navigation & Page Control
```bash
agent-browser open <url>           # Navigate to URL
agent-browser back                 # Go back
agent-browser forward              # Go forward
agent-browser reload               # Reload page
agent-browser wait <selector>      # Wait for element
agent-browser wait 3000            # Wait 3 seconds
```

### Element Interaction
```bash
agent-browser click <sel>          # Click element
agent-browser dblclick <sel>       # Double-click
agent-browser fill <sel> <text>    # Clear and fill input
agent-browser type <sel> <text>    # Type into element
agent-browser press Enter          # Press key
agent-browser check <sel>          # Check checkbox
agent-browser uncheck <sel>        # Uncheck checkbox
agent-browser select <sel> <val>   # Select dropdown option
agent-browser hover <sel>          # Hover over element
agent-browser upload <sel> <file>  # Upload file
```

### Scrolling
```bash
agent-browser scroll down 500      # Scroll down 500px
agent-browser scroll up            # Scroll up
agent-browser scrollintoview <sel> # Scroll element into view
```

### Information Extraction
```bash
agent-browser get text <sel>       # Get text content
agent-browser get html <sel>       # Get innerHTML
agent-browser get value <sel>      # Get input value
agent-browser get attr href <sel>  # Get attribute
agent-browser get url              # Get current URL
agent-browser get title            # Get page title
agent-browser get count <sel>      # Count matching elements
```

### Element State Checks
```bash
agent-browser is visible <sel>     # Check if visible
agent-browser is enabled <sel>     # Check if enabled
agent-browser is checked <sel>     # Check if checked
```

### Screenshots & PDF
```bash
agent-browser screenshot [path]    # Screenshot (default: screenshot.png)
agent-browser screenshot --full    # Full page screenshot
agent-browser pdf output.pdf       # Save as PDF
```

### Semantic Element Finding
```bash
# Find by role (button, link, textbox, etc.)
agent-browser find role button click --name "Submit"

# Find by text content
agent-browser find text "Login" click

# Find by label
agent-browser find label "Username" fill "john"

# Find by placeholder
agent-browser find placeholder "Search..." type "query"

# Find by test ID
agent-browser find testid "submit-btn" click

# Find by position
agent-browser find first button click
agent-browser find last link click
agent-browser find nth 2 button click
```

### Browser Configuration
```bash
# Set viewport size
agent-browser set viewport 1920 1080

# Emulate device
agent-browser set device "iPhone 13"

# Set geolocation
agent-browser set geo 37.7749 -122.4194

# Toggle offline mode
agent-browser set offline on

# Set color scheme
agent-browser set media dark

# Set custom headers
agent-browser set headers '{"Authorization":"Bearer token"}'
```

### Session Management
```bash
agent-browser --session <name> <command>  # Use specific session
agent-browser session list                # List active sessions
agent-browser session                     # Show current session
```

### Cookies & Storage
```bash
agent-browser cookies                     # View all cookies
agent-browser cookies set name value      # Set cookie
agent-browser storage local               # View localStorage
agent-browser storage session             # View sessionStorage
```

### Network Control
```bash
agent-browser network requests            # View requests
agent-browser network route <url> --abort # Block requests
agent-browser network route <url> --body '{...}'  # Mock response
```

### Authentication & State
```bash
agent-browser state save auth.json        # Save auth state
agent-browser state load auth.json        # Restore auth state
```

### Debugging
```bash
agent-browser console                     # View console messages
agent-browser errors                      # View page errors
agent-browser highlight <sel>             # Highlight element
agent-browser trace start trace.zip       # Start recording trace
```

## Practical Workflows

### Workflow 1: Login & Extract Data
```bash
# Start session
agent-browser --session login open "https://app.example.com/login"

# Get snapshot to see form structure
agent-browser --session login snapshot -i

# Fill login form
agent-browser --session login find label "Email" fill "user@example.com"
agent-browser --session login find label "Password" fill "secret123"
agent-browser --session login find role button click --name "Sign In"

# Wait for dashboard to load
agent-browser --session login wait ".dashboard"

# Extract data
agent-browser --session login get text ".user-profile"

# Save authentication state for reuse
agent-browser --session login state save auth.json

# Take screenshot
agent-browser --session login screenshot dashboard.png
```

### Workflow 2: Form Automation
```bash
# Open form
agent-browser open "https://example.com/contact"

# Get interactive elements
agent-browser snapshot -i

# Fill form using semantic locators
agent-browser find label "Name" fill "John Doe"
agent-browser find label "Email" fill "john@example.com"
agent-browser find label "Message" fill "Hello, this is a test message."

# Select dropdown
agent-browser find label "Country" select "United States"

# Upload file
agent-browser find label "Attachment" upload "/path/to/file.pdf"

# Check checkbox
agent-browser find label "I agree to terms" check

# Submit
agent-browser find role button click --name "Submit"

# Wait for confirmation
agent-browser wait ".success-message"
agent-browser get text ".success-message"
```

### Workflow 3: Web Scraping
```bash
# Open page
agent-browser open "https://news.example.com"

# Scroll to load more content
agent-browser scroll down 1000

# Get all article titles
agent-browser get text "article h2"

# Take full page screenshot
agent-browser screenshot --full news.png

# Get page as PDF
agent-browser pdf news.pdf
```

### Workflow 4: Testing Web App
```bash
# Start fresh session
agent-browser --session test open "http://localhost:3000"

# Set viewport for desktop
agent-browser --session test set viewport 1920 1080

# Test navigation
agent-browser --session test find text "Features" click
agent-browser --session test get url  # Verify URL

# Test dark mode
agent-browser --session test set media dark
agent-browser --session test screenshot dark-mode.png

# Test mobile view
agent-browser --session test set device "iPhone 13"
agent-browser --session test screenshot mobile.png

# Check console for errors
agent-browser --session test console
agent-browser --session test errors
```

### Workflow 5: Multi-Step Automation with State
```bash
# Step 1: Login and save state
agent-browser open "https://app.example.com/login"
agent-browser find label "Email" fill "user@example.com"
agent-browser find label "Password" fill "secret123"
agent-browser find role button click --name "Login"
agent-browser wait ".dashboard"
agent-browser state save logged-in.json

# Step 2: Later, restore state and continue
agent-browser open "https://app.example.com"
agent-browser state load logged-in.json
agent-browser open "https://app.example.com/dashboard"
# You're now logged in without re-entering credentials
```

## Best Practices

1. **Always get snapshot first**: Use `snapshot` to understand page structure before interacting
2. **Prefer semantic locators**: Use `find role/text/label` over CSS selectors for reliability
3. **Use element references**: `@e` references from snapshot are more stable than selectors
4. **Wait for elements**: Use `wait <selector>` before interacting with dynamic content
5. **Use sessions for isolation**: Different tasks/users should use separate sessions
6. **Save authentication state**: Reuse `state save/load` to avoid repeated logins
7. **Filter snapshots**: Use `-i`, `-c`, `-d`, `-s` flags to reduce snapshot output
8. **Check element state**: Use `is visible/enabled/checked` before interacting
9. **Handle errors gracefully**: Check `console` and `errors` when things go wrong
10. **Take screenshots for debugging**: Visual confirmation is valuable

## Common Patterns

### Pattern: Wait for AJAX/Dynamic Content
```bash
agent-browser click "#load-more"
agent-browser wait ".new-content"  # Wait for new elements
agent-browser get text ".new-content"
```

### Pattern: Handle Dropdowns
```bash
agent-browser select "#country" "United States"
# or with semantic locator
agent-browser find label "Country" select "United States"
```

### Pattern: Keyboard Navigation
```bash
agent-browser press Tab
agent-browser press Tab
agent-browser press Enter
# or with modifiers
agent-browser press Control+a
agent-browser press Shift+Tab
```

### Pattern: Verify Element State
```bash
if agent-browser is visible "#success-msg"; then
  echo "Success message appeared"
  agent-browser get text "#success-msg"
fi
```

### Pattern: Extract Multiple Elements
```bash
# Get count first
COUNT=$(agent-browser get count ".product-item")
echo "Found $COUNT products"

# Get all texts (returns newline-separated)
agent-browser get text ".product-title"
```

## Troubleshooting

### Browser won't start
```bash
# Reinstall with dependencies
agent-browser install --with-deps

# Use custom Chrome/Chromium
agent-browser --executable-path /usr/bin/chromium open example.com
```

### Element not found
```bash
# Get snapshot to verify selectors
agent-browser snapshot -s "#container"

# Wait for element to appear
agent-browser wait <selector>

# Use semantic locators instead
agent-browser find text "Button Text" click
```

### Network issues
```bash
# Check if offline mode is on
agent-browser set offline off

# Clear cookies
agent-browser cookies
```

## Advanced: JavaScript Execution
```bash
# Run custom JavaScript
agent-browser eval "document.title"
agent-browser eval "document.querySelectorAll('a').length"
```

## Advanced: CDP Connection
```bash
# Connect to existing Chrome instance
chrome --remote-debugging-port=9222
agent-browser connect 9222
```

## Examples

### Example 1: GitHub Login & Star Repository
```bash
agent-browser open "https://github.com/login"
agent-browser find label "Username or email" fill "username"
agent-browser find label "Password" fill "password"
agent-browser find role button click --name "Sign in"
agent-browser wait ".Header"
agent-browser open "https://github.com/vercel-labs/agent-browser"
agent-browser find role button click --name "Star"
agent-browser screenshot starred.png
```

### Example 2: Google Search
```bash
agent-browser open "https://google.com"
agent-browser find role searchbox type "agent-browser github"
agent-browser press Enter
agent-browser wait "#search"
agent-browser screenshot results.png
agent-browser get text "h3"
```

### Example 3: E-commerce Price Monitoring
```bash
agent-browser open "https://shop.example.com/product/123"
PRICE=$(agent-browser get text ".price")
echo "Current price: $PRICE"
agent-browser screenshot product.png
```

## Tips for AI Agents

1. **Start with snapshot**: Always begin with `snapshot -i` to see interactive elements
2. **Use semantic finding**: `find role/text/label` is more reliable than CSS selectors
3. **Chain commands**: Build complex workflows by chaining commands
4. **Verify actions**: Use `get text` or `screenshot` to confirm actions succeeded
5. **Handle timing**: Use `wait` for dynamic content, not hardcoded sleeps
6. **Session isolation**: Use `--session` for parallel browser instances
7. **State persistence**: Use `state save/load` for multi-step workflows
8. **Debug with snapshots**: When stuck, take fresh snapshot to see current state

## Requirements

- Node.js installed
- Chromium (auto-installed via `agent-browser install`)
- Linux users may need: `agent-browser install --with-deps`

## Global Options

```bash
agent-browser --session <name> <command>     # Use specific session
agent-browser --headers <json> <command>     # Set HTTP headers
agent-browser --executable-path <path> <cmd> # Use custom browser
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hungson175) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
