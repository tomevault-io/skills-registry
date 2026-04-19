---
name: ui-preview
description: Preview and screenshot local dev servers URLs. Use when asked to view UI components, take screenshots of or inspect the web/server apps. Use when this capability is needed.
metadata:
  author: thesobercoder
---

# UI Preview Skill

Preview local dev servers URLs using Chrome DevTools.

## Available Tools

- `navigate_page` - Navigate to a URL (supports url, back, forward, reload)
- `new_page` - Open a new browser tab and load a URL
- `list_pages` - Get a list of pages open in the browser
- `select_page` - Select a page as context for future tool calls
- `click` - Clicks on the provided element (supports double click)
- `fill` - Type text into an input, text area, or select an option from a `<select>` element
- `hover` - Hover over the provided element
- `press_key` - Press a key or key combination. Use this when other input methods like fill() cannot be used (e.g., keyboard shortcuts, navigation keys, or special key combinations)
- `handle_dialog` - If a browser dialog was opened, use this command to handle it (accept/dismiss)
- `wait_for` - Wait for the specified text to appear on the selected page
- `take_snapshot` - Take a text snapshot of the currently selected page based on the a11y tree. The snapshot lists page elements with unique IDs
- `take_screenshot` - Take a screenshot of the page or element
- `evaluate_script` - Evaluate a JavaScript function inside the currently selected page. Returns the response as JSON so returned values have to be JSON-serializable
- `list_console_messages` - List all console messages for the currently selected page since the last navigation
- `get_console_message` - Gets a console message by its ID

## Workflow

- Navigate to the target URL using `navigate_page`
- Wait for the desired element to appear using `wait_for`
- Take a snapshot of the page structure using `take_snapshot`
- Interact with elements using `click`, `fill`, `hover`, or `press_key`
- Handle any dialogs using `handle_dialog`
- Screenshot the page using `take_screenshot`
- Inspect console logs using `list_console_messages` / `get_console_message`

## Local Dev URLs

Navigate to `http://localhost:3000` for the web dev server.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thesobercoder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
