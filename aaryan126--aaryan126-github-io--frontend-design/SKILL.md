---
name: frontend-design
description: Design modern, professional frontend interfaces. Use when creating UI, styling components, improving visual design, or when user mentions "make it look better", "modern design", "professional UI", or "revamp". Use when this capability is needed.
metadata:
  author: aaryan126
---

# Frontend Design Skill

Build production-grade frontend interfaces that reject generic "AI aesthetics" in favor of intentional, distinctive design.

## Design Philosophy

Before writing any code, establish:
- **Purpose**: What is this interface trying to achieve?
- **Tone**: Professional, playful, bold, minimal, luxurious?
- **Differentiation**: What makes this stand out from generic templates?

> "Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work - the key is intentionality, not intensity."

## Typography

**DO:**
- Use characterful, unexpected font pairings
- Pair a distinctive display font with a refined body font
- Consider: Clash Display, Cabinet Grotesk, Satoshi, Space Grotesk, Outfit, Plus Jakarta Sans, General Sans, Switzer
- Use fluid typography with `clamp()` for responsive sizing
- Create clear hierarchy: hero (4-6rem), headings (2-3rem), body (1-1.125rem)

**AVOID:**
- Generic fonts: Arial, Helvetica, Inter (overused), Roboto
- System font stacks as primary typography
- More than 2-3 font families

## Color & Theme

**DO:**
- Commit to a cohesive aesthetic with CSS custom properties
- Use dominant colors with sharp accents (not evenly distributed palettes)
- Create depth with subtle gradients and layered backgrounds
- Support dark/light modes with semantic color tokens

**AVOID:**
- Cliched AI color schemes (purple-blue gradients everywhere)
- Pure black (#000) on white (#fff) - use subtle off-whites and rich darks
- Random accent colors without purpose

**Example palette structure:**
```css
:root {
  --color-surface: #fafafa;
  --color-surface-elevated: #ffffff;
  --color-text-primary: #1a1a1a;
  --color-text-secondary: #6b6b6b;
  --color-accent: #2563eb;
  --color-accent-subtle: #dbeafe;
}

[data-theme="dark"] {
  --color-surface: #0f0f0f;
  --color-surface-elevated: #1a1a1a;
  --color-text-primary: #fafafa;
  --color-text-secondary: #a0a0a0;
}
```

## Motion & Animation

**DO:**
- Focus on high-impact moments: page load reveals, state transitions
- Use staggered animations for lists and grids
- Prefer CSS animations for performance
- Add micro-interactions on hover/focus states
- Use `prefers-reduced-motion` for accessibility

**AVOID:**
- Animations everywhere (creates chaos)
- Slow, sluggish transitions (keep under 300ms for interactions)
- Motion that blocks user intent

**Example stagger pattern:**
```css
.card {
  animation: fadeInUp 0.6s ease-out backwards;
}
.card:nth-child(1) { animation-delay: 0.1s; }
.card:nth-child(2) { animation-delay: 0.2s; }
.card:nth-child(3) { animation-delay: 0.3s; }

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
}
```

## Spatial Composition

**DO:**
- Use asymmetry and intentional imbalance
- Create visual rhythm with varying section heights
- Use generous whitespace (more than you think)
- Break the grid occasionally for emphasis
- Overlap elements for depth

**AVOID:**
- Everything perfectly centered
- Uniform spacing everywhere
- Predictable 12-column grid for everything

## Visual Details

**DO:**
- Add texture through subtle patterns or grain
- Use shadows with colored tints (not pure gray)
- Create glass/frosted effects for overlays
- Add borders with purpose (not everywhere)

**Example elevated shadow:**
```css
.card {
  box-shadow:
    0 1px 2px rgba(0, 0, 0, 0.05),
    0 4px 12px rgba(0, 0, 0, 0.1);
}

.card:hover {
  box-shadow:
    0 4px 8px rgba(0, 0, 0, 0.08),
    0 12px 32px rgba(0, 0, 0, 0.12);
}
```

## React + Vite Specifics (This Project)

This portfolio uses:
- **React 19** with functional components
- **Vanilla CSS** with custom properties (no Tailwind)
- **CSS data attributes** for theming: `[data-theme="dark"]`
- **Font Awesome 6** for icons
- **Google Fonts** (currently Inter - consider upgrading)

When modifying styles:
1. Use existing CSS custom properties in `src/index.css`
2. Follow the established component structure in `src/components/`
3. Maintain dark mode support with `[data-theme="dark"]` selectors
4. Use the existing animation patterns (fadeInUp, etc.)

## Implementation Checklist

When designing/redesigning UI:
- [ ] Define the visual direction before coding
- [ ] Select intentional typography (not defaults)
- [ ] Establish color system with CSS variables
- [ ] Plan key animation moments
- [ ] Ensure mobile responsiveness
- [ ] Test both light and dark themes
- [ ] Verify accessibility (contrast, focus states)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aaryan126) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
