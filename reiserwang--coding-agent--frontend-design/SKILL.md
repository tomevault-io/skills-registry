---
name: frontend-design
description: Create distinctive, production-grade frontend interfaces with high design quality. Use this skill when the user asks to build web components, pages, or applications. Generates creative, polished code that avoids generic AI aesthetics. Consumes design tokens when available. Use when this capability is needed.
metadata:
  author: reiserwang
---

This skill guides creation of distinctive, production-grade frontend interfaces that avoid generic "AI slop" aesthetics. Implement real working code with exceptional attention to aesthetic details and creative choices.

The user provides frontend requirements: a component, page, application, or interface to build. They may include context about the purpose, audience, or technical constraints.

---

## Component Polish Fast Track (for Trivial Tweaks)
If a frontend update is **Trivial**, you may skip the full Pre-Flight and Design Thinking phases.

**Trivial Frontend Criteria:**
- Minor CSS adjustments (padding, margins, simple colors).
- Fixing typos in HTML/JSX text.
- Adding a simple class or ID for styling.
- Adjusting a single animation duration or easing.
- Formatting/indentation fixes in frontend files.

**Fast Track Flow:**
1. **Announce:** "I'm using the Frontend Fast Track for a trivial tweak: [Description]."
2. **Apply:** Make the change directly to the file.
3. **Verify:** Perform a quick visual check (if a screenshot tool is available) or manual code review. Skip the full component checklist unless the change affects accessibility or core layout.

---

## Pre-Flight: Design System Integration

Before writing a single line of CSS, check:
1. Does `workspace/DESIGN.md` exist? → Load brand guidelines and consume those decisions.
2. Does a `tokens/` directory exist? → Import `tokens/platforms/web.css` (CSS custom properties). Do NOT redefine values already tokenized.
3. Are there existing components? → Extend, don't duplicate. Reuse tokens, not values.

If no design system exists and the task is large enough, suggest running `design-system:create` first.

## Design Thinking

Before coding, understand the context and commit to a BOLD aesthetic direction:
- **Purpose**: What problem does this interface solve? Who uses it?
- **Tone**: Pick an extreme: brutally minimal, maximalist chaos, retro-futuristic, organic/natural, luxury/refined, playful/toy-like, editorial/magazine, brutalist/raw, art deco/geometric, soft/pastel, industrial/utilitarian, etc.
- **Constraints**: Technical requirements (framework, performance, accessibility).
- **Differentiation**: What makes this UNFORGETTABLE? What's the one thing someone will remember?

**CRITICAL**: Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work - the key is intentionality, not intensity.

Then implement working code (HTML/CSS/JS, React, Vue, etc.) that is:
- Production-grade and functional
- Visually striking and memorable
- Cohesive with a clear aesthetic point-of-view
- Meticulously refined in every detail
- Accessible by default (WCAG 2.1 AA)

## Design Token Usage

When tokens exist, always use them. When they don't, define them inline as CSS custom properties:

```css
:root {
  /* Colors */
  --color-primary: #1a1a2e;
  --color-accent: #e94560;
  --color-surface: #16213e;
  --color-text: #eaeaea;

  /* Typography */
  --font-display: 'Playfair Display', serif;
  --font-body: 'DM Sans', sans-serif;

  /* Spacing (4px base) */
  --space-1: 4px;
  --space-2: 8px;
  --space-4: 16px;
  --space-8: 32px;
  --space-16: 64px;

  /* Radii */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 16px;
  --radius-full: 9999px;
}
```

**Rule:** No magic numbers in component CSS. All color, spacing, typography, and radius values reference a CSS variable.

## Accessibility (WCAG 2.1 AA — Always On)

Every component must:
- [ ] Pass contrast check: text ≥ 4.5:1, large text ≥ 3:1, UI components ≥ 3:1
- [ ] Be keyboard navigable (Tab, Enter, Space, Escape, arrow keys as appropriate)
- [ ] Have a visible focus indicator (`outline: 2px solid var(--color-accent); outline-offset: 2px`)
- [ ] Use semantic HTML (`<button>`, `<nav>`, `<main>`, `<article>`, not `<div>` for everything)
- [ ] Have meaningful labels (visible text or `aria-label` — never icon-only buttons without labels)
- [ ] Respect `prefers-reduced-motion`:
  ```css
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important;
      transition-duration: 0.01ms !important;
    }
  }
  ```
- [ ] Touch targets: minimum 44×44px for interactive elements

## Frontend Aesthetics Guidelines

Focus on:
- **Typography**: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics; unexpected, characterful font choices. Pair a distinctive display font with a refined body font.
- **Color & Theme**: Commit to a cohesive aesthetic. Use CSS custom properties for ALL values. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Always define a dark mode variant via `@media (prefers-color-scheme: dark)` or `[data-theme="dark"]`.
- **Motion**: Use animations for effects and micro-interactions. Prioritize CSS-only solutions for HTML. Use Motion library for React when available. Focus on high-impact moments: one well-orchestrated page load with staggered reveals creates more delight than scattered micro-interactions. Always wrap in `prefers-reduced-motion`.
- **Spatial Composition**: Unexpected layouts. Asymmetry. Overlap. Diagonal flow. Grid-breaking elements. Generous negative space OR controlled density.
- **Backgrounds & Visual Details**: Create atmosphere and depth rather than defaulting to solid colors. Add contextual effects and textures that match the overall aesthetic.

**NEVER use generic AI-generated aesthetics like:**
- Overused font families (Inter, Roboto, Arial, system fonts)
- Cliched color schemes (particularly purple gradients on white backgrounds)
- Predictable layouts and component patterns
- Cookie-cutter design that lacks context-specific character
- Hard-coded hex values, pixel values, or font names in component CSS

Interpret creatively and make unexpected choices that feel genuinely designed for the context. No design should be the same. Vary between light and dark themes, different fonts, different aesthetics. NEVER converge on common choices across generations.

**IMPORTANT**: Match implementation complexity to the aesthetic vision. Maximalist designs need elaborate code with extensive animations and effects. Minimalist or refined designs need restraint, precision, and careful attention to spacing, typography, and subtle details.

Remember: Extraordinary creative work is possible. Don't hold back, show what can truly be created when thinking outside the box and committing fully to a distinctive vision.

## Component Output Checklist

Before handing off any component:
- [ ] All values use CSS custom properties (no magic numbers)
- [ ] Contrast ratios validated for all text/background pairs
- [ ] Focus styles defined and visible
- [ ] `prefers-reduced-motion` guard on all animations
- [ ] Semantic HTML structure (no unnecessary `<div>` nesting)
- [ ] Touch targets ≥ 44×44px
- [ ] Works without JavaScript (progressive enhancement where possible)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reiserwang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
