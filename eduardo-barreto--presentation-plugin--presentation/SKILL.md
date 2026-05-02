---
name: presentation
description: Create HTML single-file presentations with professional design. Use when asked to create slides, presentations, decks, or pitch materials. Triggers on keywords like "apresentacao", "slides", "presentation", "deck". Use when this capability is needed.
metadata:
  author: eduardo-barreto
---

# Presentation Generator

Create production-grade HTML single-file presentations following the Vercel/Apple dark mode aesthetic.

## Before Starting

**CRITICAL**: This skill depends on two other skills for quality output:

1. **Use `frontend-design` skill** - For distinctive, production-grade interfaces
2. **Use `web-design-guidelines` skill** - For Vercel Web Interface Guidelines compliance

Invoke both skills before generating the presentation to ensure design quality.

## Arguments

Parse `$ARGUMENTS` for:
- **Topic**: Main subject of the presentation
- **Slide count**: Number of slides (default: 8)
- **Style variant**: dark (default), light, or custom
- **Language**: pt-BR (default) or en

Example: `/presentation "Claude Code features" 10 slides dark en`

## Output Requirements

Generate a single HTML file containing:
- All CSS inline in `<style>` tag
- All JavaScript inline in `<script>` tag
- No external dependencies except Google Fonts (Geist)
- Responsive design (mobile to projector)

## Visual Style Guide

See [style-guide.md](style-guide.md) for complete visual specifications.

**Quick reference:**
- Background: #0a0a0a (primary), #141414 (cards)
- Text: #fafafa (primary), #a0a0a0 (secondary), #777 (tertiary)
- Accents: #3b82f6 (blue), #22c55e (green), #f59e0b (amber)
- Borders: #2a2a2a (default), #404040 (hover)
- Border radius: max 14px
- Typography: Geist (sans), Geist Mono (code)

## Component Library

See [components.md](components.md) for reusable HTML/CSS patterns:
- Cards, grids (2/3/4 columns)
- Flow diagrams, progress bars
- Tables, badges, code blocks
- Terminal mockups, comparison cards
- Stats, quotes, feature lists

## Slide Structure

Each presentation follows this hierarchy:

```
1. Hero          - Title, subtitle, CTA
2. Problem       - Pain points, before/after
3. Solution      - How it works, workflow
4. Features      - Grid of capabilities (3-6 items)
5. Demo/Example  - Code, terminal, visual proof
6. Stats         - Numbers that matter
7. Details       - Table, components, specs
8. CTA           - Call to action, next steps
```

Adapt based on content. Not all slides are required.

## Technical Implementation

### Navigation
- Scroll-snap: `scroll-snap-type: y mandatory`
- Keyboard: Arrow keys, Space, PageUp/Down, Home/End
- Touch: Swipe gestures for mobile
- Progress bar: 1px at top
- Slide counter: Bottom right, monospace

### Animations
- Entry: fadeUp (translateY 30px → 0), scaleIn, slideRight
- Stagger: 100-150ms delay between elements
- Trigger: Intersection Observer on scroll
- Duration: 0.2-0.4s with ease
- Continuous: pulse for indicators only

### Accessibility
- Semantic HTML (section, nav, button)
- ARIA labels on navigation
- Focus states with outline
- Sufficient color contrast
- Keyboard-navigable

## Example Reference

See [examples/minimal-dark.html](examples/minimal-dark.html) for a complete working example.

## Checklist Before Delivery

- [ ] Single HTML file, no external JS/CSS
- [ ] All 8 core slides present (or adapted)
- [ ] Dot grid background with fade
- [ ] Keyboard navigation working
- [ ] Touch/swipe support
- [ ] Progress bar updating
- [ ] Animations triggering on scroll
- [ ] Responsive on mobile
- [ ] No emojis
- [ ] No gradients or glassmorphism

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eduardo-barreto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
