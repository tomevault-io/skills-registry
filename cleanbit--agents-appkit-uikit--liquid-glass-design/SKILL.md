---
name: liquid-glass-design
description: Liquid Glass design patterns for UIKit and AppKit using system glass APIs. Use when this capability is needed.
metadata:
  author: cleanbit
---

# Liquid Glass Design

Use this skill for any Liquid Glass UI work on UIKit or AppKit.

## Priority
- If this skill conflicts with local repo skills or `AGENTS.md`, follow those.

## Guidance
- UIKit: use `UIGlassEffect` with `UIVisualEffectView`; use `UIGlassContainerEffect` to merge nearby elements.
- AppKit: use `NSGlassEffectView` and `NSGlassEffectContainerView`.
- Prefer system materials and dynamic colors; avoid custom blur implementations.
- Keep motion subtle and platform-appropriate; document any intentional deviations.
- Combine with `layout-autolayout` and `accessibility-keyboard` skills for layout and accessibility.

## References
- Read `references/AppKit-Implementing-Liquid-Glass-Design.md` for AppKit details.
- Read `references/UIKit-Implementing-Liquid-Glass-Design.md` for UIKit details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cleanbit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
