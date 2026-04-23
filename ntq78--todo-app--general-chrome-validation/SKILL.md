---
name: general-chrome-validation
description: Use when user provides a URL in their prompt OR when working with a /p spec doc that has a Chrome Validation Tab URL
metadata:
  author: ntq78
---

# General: Chrome Browser Validation

Chrome browser automation via `mcp__claude-in-chrome__*` tools for visual validation.

## When to Use

| Condition                                     | Action                              |
| --------------------------------------------- | ----------------------------------- |
| `/p` spec doc has `Chrome Validation Tab URL` | Use that URL after completing tasks |
| User provides URL in prompt                   | Use that URL after completing task  |
| User says "check chrome" but no URL           | Ask user for the URL                |
| No URL mentioned                              | **Do NOT** offer Chrome validation  |

## Mandatory Workflow

### 1. Get Tab Context (Always First)

```
mcp__claude-in-chrome__tabs_context_mcp
```

### 2. Get URL

**Priority:** 1) `/p` spec doc URL, 2) User prompt URL, 3) Ask user

### 3. Navigate and Validate

```
mcp__claude-in-chrome__navigate (url, tabId)
mcp__claude-in-chrome__computer (action: "screenshot", tabId)
```

### 4. Report Findings

- What you observed
- Whether implementation matches expectations
- Any issues found

## Available Tools

| Tool                    | Purpose                         |
| ----------------------- | ------------------------------- |
| `tabs_context_mcp`      | Get available tabs (call first) |
| `tabs_create_mcp`       | Create new tab                  |
| `navigate`              | Navigate to URL                 |
| `computer`              | Screenshot, click, type, scroll |
| `read_page`             | Get accessibility tree          |
| `find`                  | Find elements by description    |
| `form_input`            | Set form field values           |
| `javascript_tool`       | Execute JS in page              |
| `read_console_messages` | Read browser console            |
| `get_page_text`         | Extract text content            |

## Base URL

App runs at: `http://localhost:5173/` - assume dev server is always running.

## Common Mistakes

- ❌ Validate without URL trigger → ✅ Only validate when URL provided
- ❌ Skip `tabs_context_mcp` → ✅ Always call it first
- ❌ Guess URLs → ✅ Use exactly what user/spec provides
- ❌ Attempt login → ✅ Assume tabs are authenticated
- ❌ Reuse old tab IDs → ✅ Tab IDs change between sessions

## When NOT to Use

- User didn't provide URL and didn't mention Chrome
- Backend-only changes
- Changes verifiable via type checking alone

<!-- Last compacted: 2026-01-15 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
