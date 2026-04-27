---
name: material-3-to-material-2-theme-adapter
description: Learn how to seamlessly integrate Material Design 3's styling into your Material Design 2 components using CSS variable overrides. Use when this capability is needed.
metadata:
  author: rodydavis
---

# Material 3 to Material 2 Theme Adapter


## Overview 

How to style Material 2 components with Material 3 in CSS:

```
:root {
  --mdc-theme-primary: var(--md-sys-color-primary);
  --mdc-theme-on-primary: var(--md-sys--coloron-primary);
  --mdc-theme-background: var(--md-sys--colorbackground);
  --mdc-theme-on-background: var(--md-sys--coloron-background);
  --mdc-theme-on-surface-variant: var(--md-sys--coloron-surface-variant);
  --mdc-theme-surface-variant: var(--md-sys--colorsurface-variant);
  --mdc-theme-on-surface: var(--md-sys--coloron-surface);
  --mdc-theme-surface: var(--md-sys--colorsurface);
  --mdc-theme-text-primary-on-background: var(--md-sys--coloron-surface-variant);
  --mdc-theme-outline: var(--md-sys-color-outline);
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rodydavis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
