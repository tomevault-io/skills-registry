---
name: ui-automation
description: > Use when this capability is needed.
metadata:
  author: 1-martian-way
---

## UI Automation

You are automating a desktop UI interaction. Follow the observe-act-verify loop for every action.

### 1. Observe — Understand Current State

Before any interaction, see what is on screen:

- Use `mcp__hijak__screenshot` to capture the current state.
- Use `mcp__hijak__accessibility_tree` to get the structured element hierarchy of the target application.
- Identify the target element and its current state (enabled, focused, visible, value).

### 2. Locate — Find the Target Element

Use the most reliable identification strategy, in this priority order:

1. **Accessibility find (preferred)**: Use `mcp__hijak__accessibility_find` to locate elements by role, title, or value. This is the most robust method and survives layout changes.
2. **OCR text matching**: Use `mcp__hijak__ocr_window` to find text on screen, then derive coordinates from the OCR bounding boxes. Useful for non-standard UI controls.
3. **Coordinates (last resort)**: Only use raw coordinates from the screenshot if neither a11y nor OCR can locate the element. Coordinate-based targeting is fragile and breaks when windows move or resize.

### 3. Act — Execute the Interaction

Perform the requested action:

- **Click**: Use `mcp__hijak__mouse_click` with the element's coordinates. Specify `button` (left, right, middle) and `count` (1 for single, 2 for double) as needed. Use `mcp__hijak__accessibility_action` with action "press" when the element supports it — this is more reliable than coordinate clicking.
- **Type**: Use `mcp__hijak__keyboard_type` to enter text. First click the target field to focus it, or use `mcp__hijak__accessibility_action` with action "focus" on the field.
- **Press key**: Use `mcp__hijak__keyboard_press` for individual keys (Return, Tab, Escape, etc.).
- **Hotkey**: Use `mcp__hijak__keyboard_hotkey` for key combinations (Cmd+S, Ctrl+C, etc.).
- **Select**: For dropdowns, click the dropdown to open it, wait for the menu to appear, then click the option. Or use `mcp__hijak__accessibility_action` with action "setValue" if the element supports it.
- **Scroll**: Use `mcp__hijak__mouse_scroll` to scroll within the target area.
- **Drag**: Use `mcp__hijak__mouse_drag` for drag-and-drop operations.

### 4. Wait — Let the UI Settle

After each action, wait for the UI to respond:

- Use `mcp__hijak__wait_for_idle` to wait for the application to finish processing.
- Use `mcp__hijak__wait_for_element` if you are waiting for a specific element to appear (e.g., a dialog, a loading spinner to disappear).
- Use `mcp__hijak__wait_for_text` if you are waiting for specific text to appear on screen.
- Use `mcp__hijak__wait_for_window` if you expect a new window to open.

### 5. Verify — Confirm the Action Succeeded

After the wait, verify the result:

- Use `mcp__hijak__screenshot` to capture the post-action state.
- Compare with the pre-action screenshot to confirm the expected change occurred.
- Check the accessibility tree for the updated element state (e.g., text field now has the typed value, checkbox is now checked).
- Read any confirmation messages, status changes, or error dialogs.

### 6. Handle Errors and Retry

If the action did not produce the expected result:

- Re-examine the screenshot to understand what actually happened.
- Check if an unexpected dialog or notification appeared.
- Try an alternative identification strategy (e.g., switch from coordinates to a11y find).
- Retry the action, possibly with a longer wait or after dismissing an intervening dialog.
- Report to the user if the action cannot be completed and explain why.

### Important Guidelines

- Never perform destructive actions (closing windows, quitting apps, deleting content) without confirming with the user first.
- For multi-step flows, complete each step's full observe-act-verify cycle before moving to the next.
- If the UI changes unexpectedly mid-flow, stop and re-assess before continuing.
- Prefer accessibility actions over coordinate-based clicking whenever possible.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1-martian-way) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
