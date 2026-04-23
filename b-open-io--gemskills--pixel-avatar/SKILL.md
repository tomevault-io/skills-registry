---
name: pixel-avatar
description: Compatibility alias for legacy pixel-avatar requests. Use this skill when old prompts explicitly reference pixel-avatar, then route to avatar-portrait with a pixel-art style requirement. Use when this capability is needed.
metadata:
  author: b-open-io
---

# Pixel Avatar (Compatibility Alias)

This legacy skill name is retained for backward compatibility.

Primary skill: `avatar-portrait`.

When this alias triggers:
- Follow the `avatar-portrait` workflow.
- Set style requirements to pixel art by default (e.g. retro 16-bit, visible pixel structure).
- Preserve likeness from the reference photo.

If the user asks for a non-pixel style, use `avatar-portrait` directly and apply their requested style.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/b-open-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
