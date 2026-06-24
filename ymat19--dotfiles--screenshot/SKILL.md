---
name: screenshot
description: Analyze screenshots from the clipboard. Use when the user asks about a screenshot or wants to analyze a copied image. Use when this capability is needed.
metadata:
  author: ymat19
---

# Screenshot Analysis Skill

A skill for analyzing screenshots from the clipboard.

## Instructions

When the user asks about a screenshot:

1. **Get the image from clipboard**
   - Run `wl-paste > /tmp/clipboard-screenshot.png` to save the clipboard image to a temp file.
   - If the user provides a specific file path, use that instead.

2. **Read the image using the Read tool**
   - Read `/tmp/clipboard-screenshot.png` (or the provided path)
   - Carefully examine the image content
   - Check UI elements, error messages, text, and visual indicators

3. **Analyze and respond**
   - Clearly describe what you see
   - Identify issues or areas for improvement
   - Provide specific solutions or suggestions

## Common Use Cases

- Debugging UI bugs or layout issues
- Analyzing error messages
- Understanding application states
- Creating documentation or explanations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ymat19) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
