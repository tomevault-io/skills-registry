---
name: web-frontend-standards
description: Web frontend standards for accessibility, performance, and responsive design Use when this capability is needed.
metadata:
  author: seqis
---

# Web Frontend Standards Skill

## Overview

Design system foundations, dark mode implementation, PWA best practices, and accessibility standards for web interface development. This skill provides the **domain knowledge** for building consistent, accessible web UIs.

## Type

standards / domain-knowledge

## When to Use

**Trigger this skill when:**
- Building any web interface (PWA, SPA, dashboard, admin panel)
- Implementing dark mode / theme switching
- Setting up design tokens and CSS variables
- Ensuring WCAG accessibility compliance
- Creating component patterns for a design system

**Keywords:** web UI, frontend, dark mode, light mode, theme, design tokens, CSS variables, PWA, progressive web app, accessibility, WCAG, components, design system

## Design Tokens Foundation

**Never use raw hex values in components.** Always use semantic tokens.

### Color System

```css
:root {
  /* === LIGHT MODE (Default) === */

  /* Background hierarchy */
  --color-bg-base: #ffffff;        /* Page background */
  --color-bg-raised: #f8f9fa;      /* Cards, panels */
  --color-bg-overlay: #ffffff;     /* Modals, dropdowns */
  --color-bg-sunken: #f1f3f5;      /* Inset areas, wells */

  /* Text hierarchy */
  --color-text-primary: #1a1a2e;   /* Headings, important */
  --color-text-secondary: #4a4a68; /* Body text */
  --color-text-tertiary: #6c6c89;  /* Captions, hints */
  --color-text-disabled: #9ca3af;  /* Disabled states */
  --color-text-inverse: #ffffff;   /* On dark backgrounds */

  /* Interactive */
  --color-interactive: #2563eb;           /* Links, buttons */
  --color-interactive-hover: #1d4ed8;     /* Hover state */
  --color-interactive-active: #1e40af;    /* Active/pressed */
  --color-interactive-muted: #dbeafe;     /* Subtle backgrounds */

  /* Semantic */
  --color-success: #059669;
  --color-success-bg: #d1fae5;
  --color-warning: #d97706;
  --color-warning-bg: #fef3c7;
  --color-error: #dc2626;
  --color-error-bg: #fee2e2;
  --color-info: #0284c7;
  --color-info-bg: #e0f2fe;

  /* Borders & Dividers */
  --color-border: #e5e7eb;
  --color-border-strong: #d1d5db;
  --color-divider: #f3f4f6;

  /* Focus & Selection */
  --color-focus-ring: #3b82f6;
  --color-selection: #dbeafe;
}
```

### Typography Scale

```css
:root {
  /* Font families */
  --font-sans: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  --font-mono: ui-monospace, 'SF Mono', SFMono-Regular, Menlo, Consolas, monospace;

  /* Font sizes - modular scale (1.25 ratio) */
  --text-xs: 0.75rem;     /* 12px - captions */
  --text-sm: 0.875rem;    /* 14px - secondary */
  --text-base: 1rem;      /* 16px - body */
  --text-lg: 1.125rem;    /* 18px - emphasis */
  --text-xl: 1.25rem;     /* 20px - h4 */
  --text-2xl: 1.5rem;     /* 24px - h3 */
  --text-3xl: 1.875rem;   /* 30px - h2 */
  --text-4xl: 2.25rem;    /* 36px - h1 */

  /* Line heights */
  --leading-tight: 1.25;
  --leading-normal: 1.5;
  --leading-relaxed: 1.75;

  /* Font weights */
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;
}
```

### Spacing Scale

```css
:root {
  /* Spacing - consistent 4px base */
  --space-0: 0;
  --space-1: 0.25rem;   /* 4px */
  --space-2: 0.5rem;    /* 8px */
  --space-3: 0.75rem;   /* 12px */
  --space-4: 1rem;      /* 16px */
  --space-5: 1.25rem;   /* 20px */
  --space-6: 1.5rem;    /* 24px */
  --space-8: 2rem;      /* 32px */
  --space-10: 2.5rem;   /* 40px */
  --space-12: 3rem;     /* 48px */
  --space-16: 4rem;     /* 64px */

  /* Component-specific */
  --space-button-x: var(--space-4);
  --space-button-y: var(--space-2);
  --space-input-x: var(--space-3);
  --space-input-y: var(--space-2);
  --space-card-padding: var(--space-4);
}
```

### Shadows & Radius

```css
:root {
  /* Shadows */
  --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
  --shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1);

  /* Border radius */
  --radius-none: 0;
  --radius-sm: 0.25rem;   /* 4px - subtle */
  --radius-md: 0.375rem;  /* 6px - buttons, inputs */
  --radius-lg: 0.5rem;    /* 8px - cards */
  --radius-xl: 0.75rem;   /* 12px - modals */
  --radius-2xl: 1rem;     /* 16px - large cards */
  --radius-full: 9999px;  /* Pills, avatars */
}
```

## Dark Mode Implementation

### The Golden Rules

1. **Never use pure black (#000000)**
   - Causes "halation" effect (text appears to glow)
   - Excessive contrast causes eye strain
   - Use soft blacks: `#0f0f11`, `#121212`, `#1a1a1a`

2. **Never use pure white (#ffffff) on dark backgrounds**
   - Same halation problem in reverse
   - Use off-whites: `#f0f0f3`, `#e5e5e7`, `#e0e0e0`

3. **Elevation via lightness, not shadows**
   - In dark mode, raised surfaces are LIGHTER
   - Shadows are less visible, use sparingly
   - Create depth through background color steps

4. **Desaturate colors for dark backgrounds**
   - Saturated colors are harsh on dark
   - Reduce saturation 10-20% for dark mode
   - Exception: semantic colors (keep recognizable)

5. **Increase contrast for interactive elements**
   - Focus rings need MORE contrast in dark
   - Hover states should be clearly visible

### Dark Mode Token Overrides

```css
@media (prefers-color-scheme: dark) {
  :root {
    /* Background - elevated surfaces get LIGHTER */
    --color-bg-base: #0f0f11;        /* Soft black, not #000 */
    --color-bg-raised: #1a1a1f;      /* Cards lift via lightness */
    --color-bg-overlay: #252529;     /* Modals even lighter */
    --color-bg-sunken: #09090b;      /* Truly recessed */

    /* Text - off-white for reduced halation */
    --color-text-primary: #f0f0f3;   /* Not pure #fff */
    --color-text-secondary: #a1a1aa;
    --color-text-tertiary: #71717a;
    --color-text-disabled: #52525b;
    --color-text-inverse: #0f0f11;

    /* Interactive - slightly desaturated */
    --color-interactive: #60a5fa;
    --color-interactive-hover: #93c5fd;
    --color-interactive-active: #3b82f6;
    --color-interactive-muted: #1e3a5f;

    /* Semantic - adjusted for dark backgrounds */
    --color-success: #34d399;
    --color-success-bg: #064e3b;
    --color-warning: #fbbf24;
    --color-warning-bg: #78350f;
    --color-error: #f87171;
    --color-error-bg: #7f1d1d;
    --color-info: #38bdf8;
    --color-info-bg: #0c4a6e;

    /* Borders - subtle in dark mode */
    --color-border: #27272a;
    --color-border-strong: #3f3f46;
    --color-divider: #18181b;

    /* Focus - more visible on dark */
    --color-focus-ring: #60a5fa;
    --color-selection: #1e3a5f;

    /* Shadows - less visible, adjust opacity */
    --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.3);
    --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.4);
    --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.5);
    --shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.6);
  }
}

/* Manual toggle support */
[data-theme="dark"] {
  /* Same dark values */
}

[data-theme="light"] {
  /* Explicit light override */
}
```

### Theme Toggle Implementation

```javascript
function initTheme() {
  const stored = localStorage.getItem('theme');
  const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;

  if (stored) {
    document.documentElement.dataset.theme = stored;
  } else if (prefersDark) {
    document.documentElement.dataset.theme = 'dark';
  } else {
    document.documentElement.dataset.theme = 'light';
  }
}

function toggleTheme() {
  const current = document.documentElement.dataset.theme;
  const next = current === 'dark' ? 'light' : 'dark';
  document.documentElement.dataset.theme = next;
  localStorage.setItem('theme', next);
}

// Listen for system preference changes
window.matchMedia('(prefers-color-scheme: dark)')
  .addEventListener('change', (e) => {
    if (!localStorage.getItem('theme')) {
      document.documentElement.dataset.theme = e.matches ? 'dark' : 'light';
    }
  });
```

## WCAG Accessibility

### Contrast Requirements

| Element | Minimum Ratio | AAA Target |
|---------|---------------|------------|
| Body text | 4.5:1 | 7:1 |
| Large text (18pt+ or 14pt bold) | 3:1 | 4.5:1 |
| UI components | 3:1 | - |
| Focus indicators | 3:1 | 4.5:1 |

**Tools:** WebAIM Contrast Checker, Chrome DevTools

### Focus Indicators

```css
/* Visible focus for keyboard navigation */
:focus-visible {
  outline: 2px solid var(--color-focus-ring);
  outline-offset: 2px;
}

/* Remove default outline only when custom is applied */
:focus:not(:focus-visible) {
  outline: none;
}
```

### Motion Preferences

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

## PWA Considerations

### Manifest Theme Colors

```json
{
  "name": "Your App",
  "theme_color": "#0f0f11",
  "background_color": "#0f0f11",
  "display": "standalone"
}
```

### Meta Tags

```html
<meta name="theme-color"
      media="(prefers-color-scheme: light)"
      content="#ffffff">
<meta name="theme-color"
      media="(prefers-color-scheme: dark)"
      content="#0f0f11">

<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

## Component Patterns

### Button

```css
.btn {
  font-family: var(--font-sans);
  font-size: var(--text-sm);
  font-weight: var(--font-medium);
  padding: var(--space-button-y) var(--space-button-x);
  border-radius: var(--radius-md);
  transition: background-color 150ms, box-shadow 150ms;
}

.btn-primary {
  background: var(--color-interactive);
  color: var(--color-text-inverse);
}

.btn-primary:hover {
  background: var(--color-interactive-hover);
}

.btn-primary:focus-visible {
  outline: 2px solid var(--color-focus-ring);
  outline-offset: 2px;
}
```

### Card

```css
.card {
  background: var(--color-bg-raised);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: var(--space-card-padding);
  box-shadow: var(--shadow-sm);
}
```

### Input

```css
.input {
  background: var(--color-bg-base);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  padding: var(--space-input-y) var(--space-input-x);
  color: var(--color-text-primary);
  font-size: var(--text-base);
}

.input:focus {
  border-color: var(--color-interactive);
  outline: 2px solid var(--color-focus-ring);
  outline-offset: -1px;
}

.input::placeholder {
  color: var(--color-text-tertiary);
}
```

## Pre-Implementation Checklist

- [ ] Design tokens file created (`tokens.css` or `variables.css`)
- [ ] Color system supports both light and dark modes
- [ ] Typography scale defined
- [ ] Spacing scale defined
- [ ] Component patterns documented
- [ ] WCAG contrast verified for both themes
- [ ] Focus indicators designed
- [ ] PWA meta tags configured

## Anti-Patterns

**NEVER do these:**
- Raw hex values in components (`#3b82f6` instead of `var(--color-interactive)`)
- Pure black (`#000`) or pure white (`#fff`) in dark mode
- Shadows as primary elevation in dark mode
- Highly saturated colors on dark backgrounds
- Missing focus indicators
- Ignoring `prefers-reduced-motion`
- Forgetting theme persistence

---

*Standalone skill for web frontend standards*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
