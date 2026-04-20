---
name: accessibility-audit
description: Perform accessibility audits on React/Next.js components. Use when the user asks for accessibility review, a11y audit, WCAG compliance check, or wants to improve component accessibility. Use when this capability is needed.
metadata:
  author: hecpac
---

# Accessibility Audit Skill

## Quick Start

When performing an accessibility audit:

1. Read the component file(s)
2. Check against the checklist below
3. Provide findings with severity levels
4. Suggest specific code fixes

## Audit Checklist

### ARIA & Semantics
- [ ] Sections have `aria-labelledby` or `aria-label`
- [ ] Images have descriptive `alt` text (not empty)
- [ ] Decorative icons have `aria-hidden="true"`
- [ ] Interactive elements use native HTML (`<button>`, `<a>`)
- [ ] Headings follow logical hierarchy (h1 → h2 → h3)
- [ ] Lists use `<ul>/<ol>` with `<li>` elements
- [ ] Forms have associated labels (`<label htmlFor>`)

### Keyboard Navigation
- [ ] All interactive elements are focusable
- [ ] Focus order is logical (follows visual order)
- [ ] Focus is visible (`focus-visible:ring-2`)
- [ ] Escape closes modals/dropdowns
- [ ] Arrow keys navigate lists/tabs/accordions

### Motion & Animation
- [ ] Uses `useReducedMotion()` from Framer Motion
- [ ] Animations disabled when `prefers-reduced-motion: reduce`
- [ ] No auto-playing animations without pause control

### Images
- [ ] Uses `next/image` (not `<img>`)
- [ ] Alt text is descriptive and meaningful
- [ ] Decorative images have `alt=""`

### Forms
- [ ] Labels are visible or `sr-only`
- [ ] Error messages use `role="alert"`
- [ ] `aria-invalid` on fields with errors
- [ ] `aria-describedby` links errors to fields

### Dynamic Content
- [ ] Live regions use `aria-live="polite"` or `"assertive"`
- [ ] Loading states are announced
- [ ] Content changes are announced to screen readers

## Severity Levels

Use these when reporting issues:

- 🔴 **Critical**: Blocks access for users (missing alt, no keyboard access)
- 🟡 **Major**: Significant barrier (poor contrast, missing labels)
- 🟢 **Minor**: Enhancement opportunity (better ARIA, clearer focus)

## Report Template

```markdown
# Accessibility Audit: [Component Name]

## Summary
[Brief overview of findings]

## Critical Issues 🔴
1. [Issue]: [Location]
   - Problem: [Description]
   - Fix: [Code example]

## Major Issues 🟡
1. [Issue]: [Location]
   - Problem: [Description]
   - Fix: [Code example]

## Minor Issues 🟢
1. [Issue]: [Location]
   - Problem: [Description]
   - Fix: [Code example]

## Positive Findings ✅
- [What's working well]
```

## Common Fixes

### Missing ARIA Label
```tsx
// Before
<section>
  <h2>Our Services</h2>
</section>

// After
<section aria-labelledby="services-heading">
  <h2 id="services-heading">Our Services</h2>
</section>
```

### Empty Alt Text
```tsx
// Before
<Image alt="" src={project.image} />

// After
<Image alt={`${project.title} - ${project.location}`} src={project.image} />
```

### Div with role="button"
```tsx
// Before
<div role="button" onClick={handleClick}>Click</div>

// After
<button onClick={handleClick}>Click</button>
```

### Missing Reduced Motion
```tsx
// Before
<m.div initial={{ opacity: 0 }} animate={{ opacity: 1 }}>

// After
const prefersReducedMotion = useReducedMotion();
<m.div 
  initial={prefersReducedMotion ? {} : { opacity: 0 }} 
  animate={prefersReducedMotion ? {} : { opacity: 1 }}
>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hecpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
