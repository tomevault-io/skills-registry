---
name: brand-guidelines
description: Applies the Premium Home Design "Luxe Texas" brand identity (2026). Use this when styling components, choosing colors, or defining typography to maintain a sophisticated, editorial, and timeless architectural aesthetic. Use when this capability is needed.
metadata:
  author: hecpac
---

# Premium Home Design Brand Guidelines (Luxe Texas 2026)

## Overview

The brand identity follows an "Editorial Luxury" philosophy, blending the refinement of Telha Clarke with the serenity of Aman Resorts. The aesthetic is sophisticated, architectural, and timeless.

**Keywords**: luxury, editorial, architectural, premium, Texas modern, sophisticated, minimal, bronze, alabaster.

## Visual Philosophy

- **80% Neutrals**: Dominated by Alabaster and Ink Black.
- **15% Contrast**: Pure white and deep black for sharpness.
- **5% Accents**: Premium Bronze used strictly for high-value details.
- **Transitions**: Heavy and expensive feel using `duration-500 ease-out`.

## Brand Palette

### Primary Colors (Luxe Neutrals)

- **Alabaster**: `#FAFAF8` (hsl 40 20% 98%) - Main background and light surfaces.
- **Ink Black**: `#1C1C1C` (hsl 0 0% 11%) - Primary text and dark accents.
- **Warm Gray**: `#6B6B6B` (hsl 0 0% 42%) - Muted text and secondary info.
- **Void Black**: `#0A0A0A` (hsl 0 0% 4%) - Deep backgrounds and high-contrast elements.

### Accent Colors

- **Premium Bronze**: `#B8956A` (hsl 35 40% 56%) - Primary accent, CTA focus, and luxury signals.
- **Bronze Light**: `#D4C4B0` - Subtle hover states and borders.
- **Bronze Dark**: `#8B7355` - Pressed states or deep accents.

### Surface Palette

- **Silk**: `#F5F4F0` (hsl 40 12% 96%) - Card backgrounds.
- **Porcelain**: `#EDEDE8` (hsl 40 8% 94%) - Borders and secondary surfaces.

## Typography

### Hierarchy

- **Headings (H1, H2, H3)**: 
  - **Font**: Inria Serif (Light/300 weight).
  - **Style**: Elegant, editorial, tracking-tight.
  - **Usage**: Main narratives and section titles.
- **Body Text**: 
  - **Font**: Geist Sans (Regular).
  - **Style**: High legibility, modern, clean.
  - **Usage**: General content, descriptions.
- **Technical/Secondary**:
  - **Font**: Geist Mono.
  - **Usage**: Labels, years, technical specs.
- **Accent Headings**:
  - **Font**: Playfair Display.

## Luxury Components

### Spacing & Layout

- **Section Spacing**: `py-24 md:py-32 lg:py-40` (Section Premium).
- **Container**: `max-w-7xl mx-auto px-6`.
- **Atmosphere**: Generous white space (breathing room) is a requirement for luxury positioning.

### Shadows & Glows

- **Luxury Shadow**: `0 2px 16px -4px rgba(10, 10, 10, 0.06)` (Subtle, not muddy).
- **Bronze Glow**: `0 0 30px rgba(184, 149, 106, 0.12)` (Used for premium focus/interaction).

## Technical Implementation

- **CSS Variables**: Always prefer using CSS variables from `:root` (e.g., `var(--primary)`).
- **Tailwind Utility**: Use semantic colors like `bg-background`, `text-foreground`, `border-border`.
- **Accessibility**: All interactive elements must use the Bronze focus ring: `focus-visible:ring-2 focus-visible:ring-primary`.
- **Reduced Motion**: Always check `useReducedMotion()` from Framer Motion for high-end animations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hecpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
