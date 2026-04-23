---
name: webflow-page-transitions
description: Manage and implement client-side page transitions using Taxi.js. Use when creating transition animations, handling cross-page state, or debugging navigation. Use when this capability is needed.
metadata:
  author: vallafederico
---

# Webflow Page Transitions

Navigation and transition architecture using Taxi.js.

## Sequence
1. User clicks link.
2. `transitionOut` triggers `onPageOut` in current modules.
3. Modules are destroyed.
4. New content loads.
5. Modules are created and `onPageIn` triggers.
6. `onMount` runs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vallafederico) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
