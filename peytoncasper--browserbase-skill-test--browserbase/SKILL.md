---
name: browser-automation
description: Automate websites, scrape data, and deploy browser functions. Use when the user wants to interact with websites programmatically, scrape content, or create scheduled automations. Use when this capability is needed.
metadata:
  author: peytoncasper
---

# Browser Automation Skills

Complete toolkit for browser automation using Browserbase.

## Prerequisites

This skill collection uses two CLIs:

| CLI | Purpose | Install |
|-----|---------|---------|
| `browse` | Interactive sessions, navigation, scraping | `pnpm add -g @browserbasehq/browse-cli` |
| `bb` | Deploy serverless functions | Via `@browserbasehq/sdk-functions` in project |

**Note:** The `browse` CLI requires Chrome/Chromium installed on your system.

### Quick Setup

```bash
# Setup everything
bash scripts/setup-all.sh

# Or individually:
bash scripts/setup-browse.sh      # Interactive automation
bash scripts/setup-functions.sh   # Serverless functions
```

### Manual Setup

**Browse CLI (interactive automation):**
```bash
pnpm add -g @browserbasehq/browse-cli
```

**Browserbase Functions (serverless):**
```bash
# Get credentials from https://browserbase.com/settings
export BROWSERBASE_API_KEY="your_api_key"
export BROWSERBASE_PROJECT_ID="your_project_id"
```

## Skills Overview

| Skill | Purpose | CLI |
|-------|---------|-----|
| `/auth` | Handle login flows, OAuth, 2FA | browse |
| `/create` | Create new automations step-by-step | browse |
| `/fix` | Debug and fix failing automations | browse |
| `/functions` | Deploy serverless browser functions | bb |

## Quick Start

### Interactive Browser Session

The browse CLI uses a daemon architecture - the first command auto-starts Chrome, and state persists between commands:

```bash
browse goto https://example.com   # Auto-starts Chrome daemon
browse snapshot                    # Get DOM structure with refs
browse screenshot -o page.png      # Visual inspection
```

Test interactions:
```bash
browse click @0-5
browse fill @0-6 "value"
browse eval "document.querySelector('.price').textContent"
browse stop                        # Stop the daemon when done
```

### Deploy a Serverless Function

Create and deploy a function that runs in the cloud:

```bash
# Initialize project
pnpm dlx @browserbasehq/sdk-functions init my-functions-project
cd my-functions-project

# Edit .env with your credentials from https://browserbase.com/settings
# BROWSERBASE_PROJECT_ID=your_project_id
# BROWSERBASE_API_KEY=your_api_key

# Install and test
pnpm install
pnpm bb dev index.ts      # Test locally at http://127.0.0.1:14113

# Test with curl
curl -X POST http://127.0.0.1:14113/v1/functions/my-function/invoke \
  -H "Content-Type: application/json"

# Deploy
pnpm bb publish index.ts  # Returns Function ID
```

> **Note:** Function invocations are async. After invoking, poll the invocation endpoint for results.

## Common Workflows

### Scrape Data from a Website

1. Explore the site interactively:
```bash
browse session create --local
browse goto https://example.com
browse snapshot
```

2. Identify selectors and test extraction:
```bash
browse eval "document.querySelector('.price').textContent"
```

3. Create a function for repeated use - see `/create` skill

### Handle Login-Protected Content

1. Navigate to the site
2. Detect login requirement - see `/auth` skill
3. Complete authentication flow
4. Continue with automation

### Debug a Failing Automation

1. Check error logs
2. Start a debug session - see `/fix` skill
3. Compare expected vs actual state
4. Update selectors or add waits

## CLI Reference

### Browse CLI Commands

The CLI uses a daemon architecture - first command auto-starts Chrome, subsequent commands reuse the session.

| Command | Description |
|---------|-------------|
| `browse goto <url>` | Navigate to URL (auto-starts daemon) |
| `browse snapshot` | Get page accessibility tree with refs |
| `browse screenshot -o <file>` | Capture screenshot |
| `browse click <ref>` | Click element by ref (e.g., @0-5) |
| `browse fill <ref> <value>` | Fill input field |
| `browse type <text>` | Type text with keyboard |
| `browse press <key>` | Press key (Enter, Tab, etc.) |
| `browse eval <js>` | Execute JavaScript |
| `browse wait <state>` | Wait for page state (networkidle, etc.) |
| `browse network on` | Enable network capture |
| `browse network list` | List captured requests |
| `browse tabs list` | List open tabs |
| `browse stop` | Stop the browser daemon |

### Browserbase Functions CLI

| Command | Description |
|---------|-------------|
| `pnpm dlx @browserbasehq/sdk-functions init <name>` | Create new function project |
| `pnpm bb dev <file>` | Start local dev server |
| `pnpm bb publish <file>` | Deploy to Browserbase |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/peytoncasper) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
