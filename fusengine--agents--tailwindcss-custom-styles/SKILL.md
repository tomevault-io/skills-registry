---
name: tailwindcss-custom-styles
description: @utility, @variant, @apply, custom CSS Use when this capability is needed.
metadata:
  author: fusengine
---

# Custom Styles

## @utility - Create a utility
```css
@utility glass-effect {
  backdrop-filter: blur(10px);
  background: rgba(255, 255, 255, 0.1);
}
/* Usage: class="glass-effect hover:glass-effect" */
```

## @variant - Conditional style
```css
.card {
  background: white;
  @variant dark { background: #1a1a2e; }
  @variant hover { transform: scale(1.05); }
}
```

## @custom-variant - New variant
```css
@custom-variant theme-midnight (&:where([data-theme="midnight"] *));
/* Usage: theme-midnight:bg-black */
```

## @apply - Inline utilities
```css
.btn-primary {
  @apply bg-blue-500 text-white px-4 py-2 rounded-lg;
}
```

## @layer - CSS organization
```css
@layer components {
  .card { @apply bg-white shadow-md rounded-xl p-4; }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fusengine) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
