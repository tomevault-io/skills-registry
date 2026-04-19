---
name: design-system
description: name: Design System Expert Use when this capability is needed.
metadata:
  author: tachfineamnay
---
```skill
---
name: Design System Expert
description: Polymorphic design system — CSS variables, Tailwind tokens, component patterns, typography, spacing, animations.
version: 2.1.0
updated: 2026-02-28
---

# Design System — SocioPulse V2

> Verified against `app/globals.css` (1,433 lines), `tailwind.config.ts`, and component source.

---

## 1. Polymorphic Theming (CSS Variables)

### How the switch works

The `<html>` element receives `data-theme="social"` or `data-theme="medical"` based on `NEXT_PUBLIC_APP_MODE`. CSS vars change accordingly.

> ⚠️ The attribute is **`data-theme`**, NOT `data-brand`.

```css
/* globals.css — :root = Social defaults */
:root {
  --primary-h: 174;  --primary-s: 84%;  --primary-l: 45%;   /* Teal */
  --secondary-h: 239; --secondary-s: 84%; --secondary-l: 55%; /* Indigo */
  --accent-h: 245;   --accent-s: 90%;  --accent-l: 62%;     /* Blue-Violet */

  --radius-sm: 0.5rem;   --radius-md: 0.75rem;  --radius-lg: 1rem;
  --radius-xl: 1.25rem;  --radius-2xl: 1.5rem;  --radius-full: 9999px;

  --spacing-card: 1.5rem;    --spacing-section: 5rem;
  --btn-height: 2.75rem;     --btn-padding-x: 1.25rem;
  --canvas-bg: #F8F9FC;

  --transition-fast: 150ms;  --transition-normal: 250ms;

  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 16px -4px rgba(0, 0, 0, 0.08);
  --shadow-lg: 0 10px 40px -10px rgba(0, 0, 0, 0.12);

  --glow-primary: …;  --glow-secondary: …;  --glow-accent: …;
  --aurora-blob-1: …; --aurora-blob-2: …;   --aurora-blob-3: …;
}

/* Medical overrides */
[data-theme="medical"] {
  --primary-h: 340;  --primary-s: 85%;  --primary-l: 55%;   /* Rose */
  --secondary-h: 262; --secondary-s: 83%; --secondary-l: 58%; /* Violet */
  --accent-h: 330;   --accent-s: 90%;  --accent-l: 50%;     /* Hot Pink */

  --radius-sm: 0.25rem;  --radius-md: 0.375rem; --radius-lg: 0.5rem;
  --radius-xl: 0.625rem; --radius-2xl: 0.75rem; --radius-full: 0.5rem; /* Sharp! */

  --spacing-card: 1rem;      --spacing-section: 3rem;
  --btn-height: 2.5rem;      --btn-padding-x: 1rem;
  --canvas-bg: #FBF8FC;
}
```

### Dark Mode (prepared, selectors exist)

```css
[data-theme="social"].dark { /* … */ }
[data-theme="medical"].dark { /* … */ }
[data-theme="social"][data-mode="dark"] { /* … */ }
[data-theme="medical"][data-mode="dark"] { /* … */ }
```

`darkMode: ['class', 'class']` in tailwind.config.ts. Not yet activated in UI.

### shadcn/ui variables

`globals.css` also defines shadcn vars inside `@layer base`: `--background`, `--foreground`, `--card`, `--card-foreground`, `--popover`, `--popover-foreground`, `--primary`, `--primary-foreground`, `--secondary`, `--secondary-foreground`, `--muted`, `--muted-foreground`, `--accent`, `--accent-foreground`, `--destructive`, `--destructive-foreground`, `--border`, `--input`, `--ring`, `--radius`, `--chart-1` to `--chart-5`.

---

## 2. Tailwind Configuration

### Polymorphic Color Tokens (adapt to brand)

```typescript
// tailwind.config.ts
colors: {
  primary: {
    DEFAULT: 'hsl(var(--primary))',
    foreground: 'hsl(var(--primary-foreground))',
    50:  'hsl(var(--primary-h) var(--primary-s) 95%)',
    100: 'hsl(var(--primary-h) var(--primary-s) 90%)',
    200: 'hsl(var(--primary-h) var(--primary-s) 80%)',
    300: 'hsl(var(--primary-h) var(--primary-s) 70%)',
    400: 'hsl(var(--primary-h) var(--primary-s) 60%)',
    500: 'hsl(var(--primary-h) var(--primary-s) 50%)',
    600: 'hsl(var(--primary-h) var(--primary-s) var(--primary-l))',
    700: 'hsl(var(--primary-h) var(--primary-s) 35%)',
    800: 'hsl(var(--primary-h) var(--primary-s) 25%)',
    900: 'hsl(var(--primary-h) var(--primary-s) 15%)',
  },
  secondary: { 500, 600, DEFAULT, foreground },  // CSS vars --secondary-h/s/l
  accent:    { 500, 600, DEFAULT, foreground },  // CSS vars --accent-h/s/l
}
```

### Static Color Tokens (brand-agnostic, ALWAYS safe)

```typescript
brand: {  // Indigo — CTAs, Login, shared accent
  50: '#EEF2FF', 100: '#E0E7FF', 200: '#C7D2FE', 300: '#A5B4FC',
  400: '#818CF8', 500: '#6366F1', 600: '#4F46E5', 700: '#4338CA',
  800: '#3730A3', 900: '#312E81', DEFAULT: '#4F46E5'
},
live: {   // Teal — SocioLive, video features
  50: '#F0FDFA', …, 600: '#0D9488', …, 900: '#134E4A', DEFAULT: '#14B8A6'
},
alert: {  // Rose — SOS, urgent, destructive
  50: '#FFF1F2', …, 600: '#E11D48', …, 900: '#881337', DEFAULT: '#F43F5E'
},
gray: { 50–900 },  // Neutral gray scale
canvas: '#FAFAFA',  // ⚠️ Hardcoded, does NOT read --canvas-bg CSS var
```

### Border Radius Tokens

| Tailwind class | Value |
|----------------|-------|
| `rounded-theme-sm` | `var(--radius-sm)` |
| `rounded-theme-md` | `var(--radius-md)` |
| `rounded-theme-lg` | `var(--radius-lg)` |
| `rounded-theme-xl` | `var(--radius-xl)` |
| `rounded-theme-2xl` | `var(--radius-2xl)` |
| `rounded-theme-full` | `var(--radius-full)` |
| `rounded-lg` / `rounded-md` / `rounded-sm` | shadcn defaults via `--radius` |

### Box Shadow Tokens

| Tailwind class | Value |
|----------------|-------|
| `shadow-theme-sm` | `var(--shadow-sm)` |
| `shadow-theme-md` | `var(--shadow-md)` |
| `shadow-theme-lg` | `var(--shadow-lg)` |
| `shadow-theme-glow` | `var(--shadow-glow-lg)` |
| `shadow-soft` | `0 4px 16px -4px rgba(0,0,0,0.08)` |
| `shadow-soft-lg` | `0 8px 32px -8px rgba(0,0,0,0.12)` |
| `shadow-glow-sm` | `0 0 20px -5px var(--glow-primary)` |
| `shadow-glow-md` | `0 0 40px -10px var(--glow-primary), 0 0 20px -5px var(--glow-secondary)` |
| `shadow-glow-lg` | `0 0 60px -15px var(--glow-primary), …` |
| `shadow-glass` | `0 8px 32px -8px rgba(0,0,0,0.12), 0 0 0 1px rgba(255,255,255,0.1)` |

### Spacing Tokens

| Tailwind class | Value |
|----------------|-------|
| `p-card` / `m-card` | `var(--spacing-card)` — 1.5rem (Social) / 1rem (Medical) |
| `p-section` / `m-section` | `var(--spacing-section)` — 5rem (Social) / 3rem (Medical) |

### Animations

```typescript
keyframes: {
  modalIn: { '0%': { opacity: '0', transform: 'scale(0.96)' }, '100%': { … } },
}
animation: {
  'modal-in': 'modalIn 0.25s cubic-bezier(0.16, 1, 0.3, 1)',
}
```

Plugin: `tailwindcss-animate`

---

## 3. Typography

### Font Stack

```typescript
fontFamily: { sans: ['Outfit', 'Inter', 'system-ui', 'sans-serif'] }
```

- **Headings**: Outfit (Google Fonts)
- **Body**: Inter (system fallback)

### Scale

| Element | Classes |
|---------|---------|
| h1 | `text-4xl font-bold` (36px) |
| h2 | `text-3xl font-semibold` (30px) |
| h3 | `text-2xl font-semibold` (24px) |
| body | `text-base` (16px) |
| small | `text-sm` (14px) |

---

## 4. Component Patterns

### Radix UI Primitives

All interactive components use Radix for a11y: `Dialog`, `DropdownMenu`, `Tabs`, `Select`, `Tooltip`, `Popover`, etc.

```tsx
import * as Dialog from '@radix-ui/react-dialog';
```

### Framer Motion

```tsx
import { motion, AnimatePresence } from 'framer-motion';

// Dashboard uses staggered container + spring item variants
// from lib/dashboard-config.ts:
// containerVariants, itemVariants, cardHover
```

### Icons — Lucide React

```tsx
import { Heart, Users, Calendar } from 'lucide-react';
// Sizing: w-4 h-4 (sm), w-5 h-5 (md), w-6 h-6 (lg)
```

---

## 5. Responsive Breakpoints

```
sm: 640px    md: 768px    lg: 1024px    xl: 1280px    2xl: 1536px
```

Mobile-first: `p-4 md:p-6 lg:p-8`

---

## 6. Rules

### ✅ DO

- Use polymorphic classes: `bg-primary-*`, `rounded-theme-*`, `shadow-theme-*`
- Use static brand colors for semantics: `brand-*`, `live-*`, `alert-*`
- Use Radix UI for interactive elements
- Use Framer Motion for transitions
- Use `--spacing-card` / `--spacing-section` for layout rhythm

### ❌ DON'T

- Hardcode `bg-teal-*`, `text-rose-*`, `bg-indigo-*` — breaks on other brand
- Use `rounded-full` for cards — use `rounded-theme-full` (Medical = 0.5rem)
- Use arbitrary values without good reason
- Skip accessibility (always use Radix for interactive elements)
- Over-animate (keep transitions < 300ms)

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tachfineamnay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
