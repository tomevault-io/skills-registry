---
name: custom-compact
description: Compact current work with optional focus using agent-context-summariser Use when this capability is needed.
metadata:
  author: yulonglin
---

# Compact current work

## Instructions

1. **Generate Summary**: Use `@agent-context-summariser` to compact the current work.
   - Focus on preserving user instructions, clarifications, and the exact inputs/outputs/commands.
   - Incorporate any additional focus provided in the user's request.

2. **Replace Context**: Once the summary is generated, replace the current context window with this new summary using `/compact` (or the equivalent context management tool available).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yulonglin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
