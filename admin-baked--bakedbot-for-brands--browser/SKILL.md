---
name: browser-automation
description: Navigate the web, discover content, and interact with pages using a headless browser. Use when this capability is needed.
metadata:
  author: admin-baked
---

# Browser Skill

## Capabilities
- **Navigate**: Go to a URL and wait for it to load.
- **Discover**: Extract text content or data from the page.
- **Screenshot**: Capture visual proof of the page state.
- **Interact**: Click buttons, type text, and evaluate scripts.

## Usage
- Use `browser.navigate` for simple "Go here and read this" tasks.
- Use `browser.perform` for complex multi-step workflows (e.g. Login -> Click -> Discover).

## Constraints
- headless mode is always on.
- No audio/video playback.
- Heavy sites may timeout; usage of `wait` steps is encouraged.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/admin-baked) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
