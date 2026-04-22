---
name: feature-oriented-structure
description: Use when organizing code by feature or vertical slice rather than purely by technical layer.
metadata:
  author: etherallax
---

- Organize code by feature (e.g., Documents, Settings, Dashboard)
- Each feature may contain its own Views, ViewModels, and services
- Features should be as self-contained as practical
- Shared infrastructure belongs outside feature folders
- Avoid large global ViewModels or Services folders

Structural guidance:
- /Features/<FeatureName>/
  - Views
  - ViewModels
  - Services (optional)
- Cross-cutting concerns live in /Infrastructure or equivalent
- Do not reorganize existing features without explicit approval

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/etherallax) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
