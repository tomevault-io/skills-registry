---
name: design-system-creator
description: Create comprehensive design systems with OKLCH colors, typography scales, spacing systems, and shadcn/ui theming. Use when setting up design tokens, creating color palettes, or configuring the visual foundation of a website. Use when this capability is needed.
metadata:
  author: deomiarn
---

# Design System Creator

This skill provides guidance for creating production-grade design systems that form the visual foundation of high-quality websites.

## When to Use This Skill

- Setting up a new project's design tokens
- Creating or modifying color palettes
- Establishing typography systems
- Configuring shadcn/ui themes
- Analyzing inspiration images for design direction

## Core Principles

### 1. OKLCH Color System

OKLCH (Lightness, Chroma, Hue) is the modern standard for perceptually uniform colors.

**Structure:** `oklch(L C H)` or `oklch(L C H / alpha)`
- **L (Lightness):** 0-1 (0 = black, 1 = white)
- **C (Chroma):** 0-0.4+ (0 = gray, higher = more saturated)
- **H (Hue):** 0-360 degrees

**Creating Harmonious Palettes:**

```css
/* Primary color and semantic variations */
--primary: oklch(0.205 0.02 250);           /* Base */
--primary-light: oklch(0.4 0.02 250);       /* +0.2 lightness */
--primary-dark: oklch(0.1 0.02 250);        /* -0.1 lightness */

/* Analogous colors (±30° hue) */
--accent-warm: oklch(0.5 0.15 280);         /* +30° */
--accent-cool: oklch(0.5 0.15 220);         /* -30° */

/* Complementary (180° opposite) */
--complementary: oklch(0.5 0.15 70);        /* 250 + 180 = 70 */
```

**Neutral Scale Pattern:**
```css
/* Gray scale with consistent lightness steps */
--gray-50: oklch(0.985 0 0);
--gray-100: oklch(0.97 0 0);
--gray-200: oklch(0.922 0 0);
--gray-300: oklch(0.87 0 0);
--gray-400: oklch(0.708 0 0);
--gray-500: oklch(0.556 0 0);
--gray-600: oklch(0.446 0 0);
--gray-700: oklch(0.372 0 0);
--gray-800: oklch(0.269 0 0);
--gray-900: oklch(0.205 0 0);
--gray-950: oklch(0.145 0 0);
```

### 2. Typography System

**Font Pairing Rules:**
1. **Contrast Principle:** Pair fonts with distinct characteristics
   - Display (headlines): Expressive, distinctive
   - Body: Highly readable, neutral
2. **Maximum 2-3 fonts** per project
3. **Consider x-height** for readability at small sizes

**Recommended Pairings:**
| Display | Body | Style |
|---------|------|-------|
| Playfair Display | Source Sans Pro | Classic/Editorial |
| Space Grotesk | Inter | Modern/Tech |
| Fraunces | Work Sans | Warm/Friendly |
| Clash Display | Satoshi | Bold/Contemporary |
| Instrument Serif | Instrument Sans | Refined/Minimal |

**Fluid Typography Scale:**
```css
/* Uses clamp() for smooth scaling */
--text-xs: clamp(0.64rem, 0.17vw + 0.6rem, 0.75rem);
--text-sm: clamp(0.8rem, 0.17vw + 0.76rem, 0.875rem);
--text-base: clamp(1rem, 0.34vw + 0.91rem, 1.125rem);
--text-lg: clamp(1.25rem, 0.61vw + 1.1rem, 1.5rem);
--text-xl: clamp(1.563rem, 1vw + 1.31rem, 2rem);
--text-2xl: clamp(1.953rem, 1.56vw + 1.56rem, 2.5rem);
--text-3xl: clamp(2.441rem, 2.38vw + 1.85rem, 3.5rem);
--text-4xl: clamp(3.052rem, 3.54vw + 2.16rem, 4.5rem);
```

### 3. Spacing System

**Base-4 Scale:**
All spacing values should be multiples of 4px for consistency.

```css
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
--space-20: 5rem;     /* 80px */
--space-24: 6rem;     /* 96px */
```

**Section Spacing (Fluid):**
```css
--section-sm: clamp(2rem, 5vw, 4rem);
--section-md: clamp(4rem, 8vw, 8rem);
--section-lg: clamp(6rem, 12vw, 12rem);
```

### 4. shadcn/ui Theme Integration

**Required CSS Variables:**
```css
:root {
  --radius: 0.625rem;
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --card: oklch(1 0 0);
  --card-foreground: oklch(0.145 0 0);
  --popover: oklch(1 0 0);
  --popover-foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  --secondary: oklch(0.97 0 0);
  --secondary-foreground: oklch(0.205 0 0);
  --muted: oklch(0.97 0 0);
  --muted-foreground: oklch(0.556 0 0);
  --accent: oklch(0.97 0 0);
  --accent-foreground: oklch(0.205 0 0);
  --destructive: oklch(0.577 0.245 27.325);
  --border: oklch(0.922 0 0);
  --input: oklch(0.922 0 0);
  --ring: oklch(0.708 0 0);
}

.dark {
  --background: oklch(0.145 0 0);
  --foreground: oklch(0.985 0 0);
  /* ... dark mode values */
}
```

### 5. Analyzing Inspiration Images

When analyzing design inspiration:

1. **Extract Color Palette:**
   - Identify dominant colors (background, primary)
   - Note accent colors and their usage
   - Check light/dark contrast ratios

2. **Observe Typography:**
   - Headline style (serif, sans-serif, display)
   - Body text characteristics
   - Size relationships and hierarchy

3. **Study Spacing:**
   - Section padding patterns
   - Element spacing consistency
   - White space usage

4. **Note Patterns:**
   - Layout structure (grid, asymmetric, centered)
   - UI component styles
   - Decorative elements

## Output Format

When creating a design system, output:

1. **design-tokens.json** - Complete token definition
2. **tailwind.config.ts** - Extended Tailwind configuration
3. **globals.css** - CSS custom properties
4. **inspiration-analysis.json** - If analyzing images

## Quality Checklist

- [ ] All colors pass WCAG AA contrast (4.5:1 for text)
- [ ] Typography scale is fluid and responsive
- [ ] Spacing follows consistent base unit
- [ ] Dark mode colors are properly inverted
- [ ] shadcn/ui variables are complete
- [ ] Fonts are loaded efficiently (variable fonts preferred)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deomiarn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
