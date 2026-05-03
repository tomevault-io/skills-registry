---
name: mobile-ui-optimization
description: Optimizes mobile UI for smartphones including iPhone 16/17/18 Pro. Use when building responsive layouts, touch interfaces, mobile viewports, safe areas, or when user mentions mobile, smartphone, iPhone, responsive design, or touch targets. Use when this capability is needed.
metadata:
  author: applelamps
---

# Mobile UI Optimization

## Quick Reference

### Viewport Setup

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
```

### Safe Areas (iPhone Dynamic Island & Notch)

```css
:root {
  --safe-area-top: env(safe-area-inset-top);
  --safe-area-bottom: env(safe-area-inset-bottom);
  --safe-area-left: env(safe-area-inset-left);
  --safe-area-right: env(safe-area-inset-right);
}

body {
  padding-top: var(--safe-area-top);
  padding-bottom: var(--safe-area-bottom);
  padding-left: var(--safe-area-left);
  padding-right: var(--safe-area-right);
}
```

### Touch Target Minimums

- **Minimum size**: 44×44px (Apple HIG) / 48×48dp (Material Design)
- **Recommended**: 48×48px with 8px spacing between targets
- **Critical actions**: 56×56px minimum

### Typography Scale

```css
:root {
  --text-xs: clamp(0.75rem, 2.5vw, 0.875rem);   /* 12-14px */
  --text-sm: clamp(0.875rem, 3vw, 1rem);        /* 14-16px */
  --text-base: clamp(1rem, 3.5vw, 1.125rem);    /* 16-18px */
  --text-lg: clamp(1.125rem, 4vw, 1.25rem);     /* 18-20px */
  --text-xl: clamp(1.25rem, 4.5vw, 1.5rem);     /* 20-24px */
  --text-2xl: clamp(1.5rem, 5vw, 2rem);         /* 24-32px */
}
```

## Device Breakpoints

| Device | Width | Pixel Ratio |
|--------|-------|-------------|
| iPhone SE | 375px | 2x |
| iPhone 14/15/16 | 390px | 3x |
| iPhone 14/15/16 Pro | 393px | 3x |
| iPhone 14/15/16 Plus | 428px | 3x |
| iPhone 14/15/16 Pro Max | 430px | 3x |
| iPhone 17/18 Pro (expected) | 393-402px | 3x |

### Tailwind Breakpoints

```js
// tailwind.config.js
screens: {
  'xs': '375px',    // iPhone SE, small phones
  'sm': '390px',    // iPhone 14/15/16 base
  'md': '430px',    // iPhone Pro Max, large phones
  'lg': '768px',    // Tablets
  'xl': '1024px',   // Desktop
}
```

## Core Patterns

### 1. Responsive Container

```tsx
<div className="w-full max-w-screen-sm mx-auto px-4 
  pt-[env(safe-area-inset-top)] 
  pb-[env(safe-area-inset-bottom)]">
  {children}
</div>
```

### 2. Bottom Navigation (iOS Home Indicator Safe)

```tsx
<nav className="fixed bottom-0 left-0 right-0 
  bg-white border-t border-gray-200
  pb-[env(safe-area-inset-bottom)]">
  <div className="flex justify-around py-2">
    {/* Nav items with min-h-[44px] */}
  </div>
</nav>
```

### 3. Touch-Optimized Button

```tsx
<button className="min-h-[44px] min-w-[44px] px-4 py-3
  active:scale-95 transition-transform
  touch-manipulation">
  {label}
</button>
```

### 4. Scroll Container (Momentum Scrolling)

```tsx
<div className="overflow-y-auto overscroll-contain
  -webkit-overflow-scrolling: touch
  scroll-snap-type-y-mandatory">
  {children}
</div>
```

## Critical Rules

1. **NEVER** use `user-scalable=no` or `maximum-scale=1` - breaks accessibility
2. **ALWAYS** use `touch-manipulation` on interactive elements to remove 300ms delay
3. **ALWAYS** account for Dynamic Island/notch with `env(safe-area-inset-*)`
4. **NEVER** rely on hover states for critical interactions
5. **ALWAYS** provide visible focus states for keyboard/assistive technology users

## Detailed Guides

- **Viewport & Safe Areas**: See [VIEWPORT.md](VIEWPORT.md)
- **Touch Interactions**: See [TOUCH.md](TOUCH.md)
- **Typography & Readability**: See [TYPOGRAPHY.md](TYPOGRAPHY.md)
- **Performance**: See [PERFORMANCE.md](PERFORMANCE.md)
- **Forms & Inputs**: See [FORMS.md](FORMS.md)
- **Navigation Patterns**: See [NAVIGATION.md](NAVIGATION.md)
- **Testing Checklist**: See [TESTING.md](TESTING.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/applelamps) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
