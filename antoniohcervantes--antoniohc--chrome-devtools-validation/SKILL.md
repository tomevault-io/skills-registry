---
name: chrome-devtools-validation
description: Validate UI changes in the AntonioHC Next.js portfolio using the Chrome DevTools MCP. Use when asked to verify sections, layouts, light/dark theming, responsive behavior, or visual regressions on http://localhost:3000/. Use when this capability is needed.
metadata:
  author: antoniohcervantes
---

# Chrome DevTools Validation

## Overview

Use the Chrome DevTools MCP to open the local dev site and validate UI changes with snapshots, screenshots, and console checks.

## Workflow

1. Confirm MCP availability by calling `list_pages`. If it times out, ask the user to restart the MCP or increase `--protocolTimeout`.
2. Open or select `http://localhost:3000/` with `new_page` or `select_page`.
3. Navigate to the requested UI area using `evaluate_script` to find headings/labels and scroll them into view.
4. Capture evidence:
   - `take_snapshot` for the a11y tree.
   - `take_screenshot` for visual confirmation.
5. Validate:
   - Layout, spacing, typography, and imagery.
   - Light/dark theme if relevant (toggle the theme switch).
   - Responsive layout using `resize_page` (e.g., 375px width).
6. Check runtime errors with `list_console_messages` filtered to `error`.
7. Report results succinctly, noting the viewport/theme used and any issues found.

## Reporting

State whether the section looks correct or list the issues found. Include:
- The page URL.
- The viewport size and theme used.
- Any console errors or missing assets.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/antoniohcervantes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
