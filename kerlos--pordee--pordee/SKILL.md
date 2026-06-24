---
name: pordee-stats
description: > Use when this capability is needed.
metadata:
  author: kerlos
---

This skill is delivered by `hooks/pordee-stats.js` (read by `hooks/pordee-mode-tracker.js` on `/pordee-stats`). The model does not need to do anything when this skill fires — the hook returns `decision: "block"` with the formatted stats as the reason. The user sees the numbers immediately.

---
> Source: [kerlos/pordee](https://github.com/kerlos/pordee) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
