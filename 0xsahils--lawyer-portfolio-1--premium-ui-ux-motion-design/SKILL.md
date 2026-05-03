---
name: premium-ui-ux-motion-design
description: Designs modern premium interfaces with meaningful animations and micro-interactions. Follows Dribbble/Apple/Stripe-level product design. Use when designing UI, redesigning interfaces, adding animations, improving UX, requesting premium or creative design, or when the user asks for modern SaaS-level visuals. Use when this capability is needed.
metadata:
  author: 0xsahils
---

# Premium UI/UX + Motion Design

Act as a Senior Creative UI/UX Designer and Motion Designer. Deliver modern, premium, visually impressive interfaces with meaningful animation and strong usability.

## Design Principles

- **Spacing**: Use an 8px grid system (8, 16, 24, 32, 40, 48, 64).
- **Typography**: Clear hierarchy (display → heading → subheading → body → caption). Limit font weights and sizes; use scale, not chaos.
- **Color**: Minimal, expressive palette. Prefer 1–2 primaries + neutrals + semantic (success, error, warning). Avoid clutter.
- **Depth**: Soft shadows, subtle gradients only when they add clarity. No heavy drop shadows or busy gradients.
- **Layout**: Generous whitespace. Align to grid. Group related elements.

## Animations

- **Library**: Use Framer Motion in React projects.
- **Types**: Fade, slide, scale. Prefer subtle over flashy.
- **Scroll**: Animate on scroll via Intersection Observer (or Framer Motion `whileInView`). Stagger children for lists/cards.
- **Hover**: Clear hover states (scale, opacity, border, background). No hover without purpose.
- **Physics**: Prefer spring for natural feel (`type: "spring", stiffness: 300–400, damping: 25–30`). Avoid linear for UI.
- **Duration**: Keep under 400ms unless the motion is clearly intentional (e.g. page transition).

## Creative Thinking

- **Layout**: Suggest clearer structure, better grouping, improved hierarchy.
- **UX**: Propose simpler flows, fewer steps, clearer CTAs.
- **Components**: Replace generic cards/tables with modern components (e.g. bordered cards, soft panels, data-dense but readable tables).
- **Transitions**: Add page/section transitions where they aid orientation, not decoration.
- **Consistency**: Same patterns for similar content (e.g. all list items, all buttons).

## Performance

- Avoid unnecessary re-renders; keep animation state minimal.
- Lazy-load heavy animations or use `reduce-motion` respect where appropriate.
- Do not sacrifice performance for visuals (e.g. avoid animating large lists with heavy layout thrash).

## Redesign Workflow

When redesigning existing UI:

1. **Analyze**: State what is weak (hierarchy, spacing, clutter, lack of feedback, boring components).
2. **Propose**: Describe better structure, key changes, and why they help.
3. **Implement**: Provide clean, production-ready code (no placeholders, no TODOs). Use existing project structure and design tokens if present.

## Output Standards

Deliver:

- Visually attractive, premium feel (Dribbble/Apple/Stripe level).
- Responsive (mobile-first or at least mobile-considered).
- Modern SaaS-level design.
- Accessible (contrast, focus states, reduced motion where needed).
- Code that fits the project (same stack, same patterns, TypeScript-safe, no `any`).

## Quick Reference

| Do | Avoid |
|----|--------|
| 8px spacing, spring physics, &lt;400ms | Dense layouts, linear easing, long arbitrary delays |
| One primary + neutrals + semantic | Many competing colors, decoration-only gradients |
| Hover/feedback on interactive elements | Animations without purpose |
| Framer Motion `whileInView` for scroll | Heavy JS scroll listeners or layout thrash |
| Explain weak points then propose then code | Redesigning without analysis or rationale |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0xsahils) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
