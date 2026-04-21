---
name: menus
description: Troubleshooting GUI menus: color picker, spy toggle, and menu interaction issues Use when this capability is needed.
metadata:
  author: kangarko
---

# Menu Troubleshooting

## Common Mistakes

- **Title max 32 chars pre-MC 1.20** — longer titles cause errors on older versions. MC 1.20+ supports longer titles
- **Animated title causes flicker** — `restartMenu()` in a loop causes rapid refreshing. Use targeted slot updates instead of full restart when possible
- **Color/spy menu items need specific permissions** — `chatcontrol.color.{color}` for color menu items, `chatcontrol.spy.{type}` for spy menu items. Items without permission are hidden

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kangarko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
