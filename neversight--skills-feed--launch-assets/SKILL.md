---
name: launch-assets
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# /launch-assets

All launch assets. One command.

## What This Orchestrates
1. /brand-builder - if no brand-profile.yaml exists
2. /product-hunt-kit - PH launch copy + checklist
3. /og-hero-image - AI hero image for social
4. /announce - Multi-platform launch posts
5. /app-screenshots - if apps/mobile exists
Usage:
- `/launch-assets heartbeat`
- `/launch-assets caesar without screenshots`
Output:
```text
launch-assets/
  product-hunt-kit.md
  og-hero-[name].png
  announcements/
  screenshots/ (if mobile)
```
Order Matters:
Brand profile → Copy → Images → Distribution
Each step informs the next.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
