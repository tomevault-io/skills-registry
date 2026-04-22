---
name: mvvm-pattern
description: Use when applying Model–View–ViewModel separation, binding-driven UI design, and viewmodel-first interaction patterns.
metadata:
  author: etherallax
---

- Clear separation between Views, ViewModels, and domain logic
- ViewModels contain state and behavior, not UI rendering logic
- Views are binding-driven and as thin as possible
- Commands preferred over code-behind event handling
- Support for viewmodel-first workflows where appropriate

Framework guidance:
- WPF: compatible with CommunityToolkit.MVVM or equivalent
- Avalonia: compatible with CommunityToolkit.MVVM or equivalent
- Do not assume a specific MVVM framework unless explicitly stated in PROJECT or PRD

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/etherallax) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
