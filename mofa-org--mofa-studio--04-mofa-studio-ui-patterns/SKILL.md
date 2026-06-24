---
name: 04-mofa-studio-ui-patterns
description: Makepad UI patterns in MoFA Studio: live_design layout, event handling, apply_over usage, shader-driven theming, and animation lifecycles. Use when editing UI or behavior. Use when this capability is needed.
metadata:
  author: mofa-org
---

# MoFA Studio UI Patterns

## 1. Overview
Follow Makepad patterns for event handling and runtime updates. Use `apply_over` for dynamic changes and keep hover handling before action extraction.

## 2. UI workflow
1. Build layout in `live_design!` using theme constants.
2. Implement `Widget::handle_event` and call `self.view.handle_event`.
3. Handle hover events before `Event::Actions` early return.
4. Use `apply_over` for visibility and shader instance updates.
5. Use shader time or `Event::NextFrame` for animations.

## 3. References
- references/ui-workflow.md
- references/hover-and-apply-over.md
- references/ui-edge-cases.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mofa-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
