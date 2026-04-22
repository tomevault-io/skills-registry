---
name: layout-persistence
description: Use when reasoning about saving and restoring UI layout state across sessions, including docked documents,
metadata:
  author: etherallax
---

- Persist user layout state on shutdown or explicit save
- Restore layout deterministically on startup
- Support multiple documents and tool windows
- Handle missing or invalid layout data gracefully
- Avoid layout corruption across versions where possible

Framework mapping guidance:
- WPF: integrate with docking library layout serialization (e.g., AvalonDock)
- Avalonia: use framework-appropriate docking/layout persistence mechanisms
- Storage format and location are implementation details
- Do not assume JSON/XML/binary storage unless specified in PROJECT or PRD

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/etherallax) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
