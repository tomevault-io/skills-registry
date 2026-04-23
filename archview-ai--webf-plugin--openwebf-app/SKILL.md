---
name: openwebf-app
description: DEPRECATED umbrella Skill (backward compatibility). Use only for cross-cutting orchestration across multiple WebF app tasks when a request spans several capabilities (dev loop + debugging + testing + release). Prefer focused openwebf-app-* Skills. Use when this capability is needed.
metadata:
  author: archview-ai
---

# OpenWebF App Skill

## Deprecated

Prefer these focused Skills (more reliable auto-discovery, fewer conflicts):
- `openwebf-app-webf-go`
- `openwebf-app-devserver-network`
- `openwebf-app-debugging-devtools`
- `openwebf-app-testing-vitest`
- `openwebf-app-performance-js`

## Instructions

If this umbrella Skill is active, immediately route to the most relevant focused Skill based on the user’s trigger terms (e.g. “WebF Go”, “Vitest”, “DevTools”, “performance.mark”, “--host”), then follow that Skill’s playbook.

Keep changes minimal and ask one clarifying question when the request spans multiple areas.

If repo context is missing, start with `/webf:doctor` and then route to the focused Skill that best matches the user’s trigger terms.

## Examples

- “Set up Vitest in this WebF app project and add one unit test and one component test.”
- “Help me debug why WebF Go loads a blank screen from my dev server.”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archview-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
