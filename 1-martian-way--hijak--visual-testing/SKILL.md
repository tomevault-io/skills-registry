---
name: visual-testing
description: > Use when this capability is needed.
metadata:
  author: 1-martian-way
---

## Visual Testing

You are performing visual regression testing by capturing and comparing screenshots.

### 1. Capture Baseline Screenshots

When establishing a baseline (the known-good state):

- Use `mcp__hijak__screenshot` for full-screen baselines.
- Use `mcp__hijak__screenshot_window` for window-specific baselines (preferred — isolates the target app from desktop clutter).
- Use `mcp__hijak__screenshot_region` for specific UI areas (e.g., a toolbar, a form, a sidebar).
- Give each baseline a clear, descriptive name that identifies the state (e.g., "login-page-empty-form", "dashboard-with-data", "settings-dialog-general-tab").
- Use `mcp__hijak__ocr_window` to capture the text content of the baseline as a structured reference.
- Use `mcp__hijak__accessibility_tree` to capture the element hierarchy of the baseline for structural comparison.

### 2. Capture Test Screenshots

When checking for regressions against a baseline:

- Reproduce the same conditions as the baseline (same window, same application state, same data if possible).
- Use the same screenshot tool and parameters as the baseline (full screen, window, or region).
- Use `mcp__hijak__wait_for_idle` before capturing to ensure the UI has fully rendered.

### 3. Compare Screenshots

Analyze the differences between the baseline and test screenshots:

**Visual comparison:**
- Examine both screenshots side by side (you can see the images).
- Look for layout shifts, color changes, missing elements, alignment issues, font changes, spacing differences.
- Distinguish between meaningful changes and acceptable variation.

**Text comparison:**
- Use `mcp__hijak__ocr_window` on the test screenshot and compare the extracted text against the baseline OCR text.
- Identify added, removed, or changed text.
- Flag text that should be static but has changed.

**Structural comparison:**
- Use `mcp__hijak__accessibility_tree` on the test state and compare the element hierarchy against the baseline a11y tree.
- Identify added, removed, or reordered elements.
- Check for changed element properties (title, value, enabled state).

### 4. Handle Dynamic Content

Some areas of the UI change between runs and should not be flagged as regressions:

- **Timestamps and dates**: Expect these to differ; focus on format consistency rather than exact values.
- **User-specific data**: Names, avatars, counts that vary per user or session.
- **Animations and cursors**: Blinking cursors, loading spinners, animated elements.
- **Random/generated content**: UUIDs, session IDs, tokens visible in developer UIs.

When comparing, mentally mask these dynamic regions and focus on the stable structural and visual elements.

### 5. Report Results

For each comparison, provide a clear verdict:

**Pass:**
- "No visual regressions detected. The UI matches the baseline."
- Note any acceptable dynamic content differences that were excluded.

**Fail:**
- Describe each regression found with specific details:
  - What changed (element, area, property).
  - Where on screen (coordinates, element description).
  - How it changed (old value vs. new value, moved from X to Y, element missing).
- Include the relevant screenshots as evidence.
- Classify the severity:
  - **Critical**: Layout broken, elements overlapping, content missing, functionality obscured.
  - **Major**: Significant visual change (color, spacing, alignment) affecting usability.
  - **Minor**: Small cosmetic differences (1-2px shifts, slight color variation).

### 6. Iterative Testing

For multi-state visual testing:

- Navigate the application through its various states (empty, loading, populated, error, etc.).
- Capture baselines for each state.
- After code changes, re-run the same state sequence and compare each state against its baseline.
- Use `mcp__hijak__wait_for_element`, `mcp__hijak__wait_for_text`, or `mcp__hijak__wait_for_idle` between states to ensure proper rendering before capture.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1-martian-way) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
