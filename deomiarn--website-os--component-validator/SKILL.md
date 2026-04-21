---
name: component-validator
description: Validates components after implementation - TypeScript, Design Tokens, Responsive, Accessibility, Animation Performance. Use proactively after each section implementation.
metadata:
  author: deomiarn
---

# Component Validator

This skill provides checkpoint validation after implementing each component or section, catching issues early before they accumulate.

## When to Use This Skill

- **After implementing each section** (mandatory checkpoint)
- After significant component changes
- Before moving to the next page
- When debugging quality issues
- Before final verification

## Validation Checklist

### 1. TypeScript Validation

```bash
# Run type check
npx tsc --noEmit

# Expected: 0 errors
```

**Common Issues:**
| Issue | Fix |
|-------|-----|
| Missing types for props | Define interface |
| `any` usage | Replace with proper type |
| Missing return types | Add explicit returns |
| Null checks missing | Add optional chaining |

### 2. Design Token Usage

Check that components use design tokens, NOT hardcoded values:

**❌ Bad:**
```tsx
<div className="bg-blue-500 text-white rounded-lg">
```

**✅ Good:**
```tsx
<div className="bg-primary text-primary-foreground rounded-radius">
```

**Token Categories to Check:**
| Category | Token Example | Hardcoded (Bad) |
|----------|---------------|-----------------|
| Colors | `bg-primary` | `bg-blue-500` |
| Spacing | `p-section-md` | `p-24` (unless in scale) |
| Radius | `rounded-radius` | `rounded-lg` (unless configured) |
| Typography | `text-foreground` | `text-gray-900` |

### 3. Responsive Validation

Test at 3 breakpoints:

| Breakpoint | Width | What to Check |
|------------|-------|---------------|
| Mobile | 375px | Stacking, touch targets 44px |
| Tablet | 768px | Grid adjustments, spacing |
| Desktop | 1440px | Full layout, max-widths |

**Responsive Checklist:**
- [ ] No horizontal overflow at any breakpoint
- [ ] Text readable at all sizes (min 16px body)
- [ ] Touch targets minimum 44x44px on mobile
- [ ] Grid columns collapse properly
- [ ] Images responsive with `sizes` attribute

### 4. Accessibility Validation

**Must Pass:**
| Check | Requirement |
|-------|-------------|
| Color Contrast | 4.5:1 minimum (text), 3:1 (large text) |
| Focus States | Visible focus ring on all interactive elements |
| Alt Text | All images have descriptive alt text |
| Heading Order | H1 → H2 → H3 (no skipping) |
| Keyboard Nav | All interactive elements reachable |
| ARIA Labels | Complex components labeled |

**Quick Check:**
```bash
# Browser DevTools
# - Run Lighthouse Accessibility audit
# - Check with axe-core extension
```

### 5. Animation Performance

**Performance Budget:**
| Metric | Target |
|--------|--------|
| Frame Rate | 60fps (16.67ms per frame) |
| Animation Duration | 200-600ms typical |
| Stagger Delay | 50-150ms between items |

**Optimizations:**
```tsx
// ✅ Good: GPU-accelerated properties
transform: translateY(20px)
opacity: 0

// ❌ Bad: Causes layout shifts
margin-top: 20px
height: 0
```

**Framer Motion Best Practices:**
```tsx
// Use layout animations sparingly
<motion.div layout /> // Can be expensive

// Prefer transform-based animations
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5 }}
/>

// Use viewport detection
<motion.div
  initial="hidden"
  whileInView="visible"
  viewport={{ once: true, margin: "-100px" }}
/>
```

### 6. Design Quality Validation (KRITISCH)

**This is the most important validation!** A technically correct but visually generic section FAILS.

#### 6.1 Background Check

| Status | Pattern | Example |
|--------|---------|---------|
| ❌ FAIL | Plain white | `<section className="bg-white">` |
| ❌ FAIL | Plain gray | `<section className="bg-gray-50">` |
| ✅ PASS | Gradient mesh | `bg-gradient-to-br from-primary/5 via-background to-accent/5` |
| ✅ PASS | Blur shapes | `absolute w-96 h-96 bg-primary/10 rounded-full blur-3xl` |
| ✅ PASS | Distinctive solid | `bg-[#F2F0E9]` (from inspiration palette) |
| ✅ PASS | Pattern/texture | Noise overlay, geometric patterns |

#### 6.2 Typography Hierarchy Check

| Status | Issue | Example |
|--------|-------|---------|
| ❌ FAIL | Flat hierarchy | Headlines all `text-2xl` |
| ❌ FAIL | Generic fonts | `font-sans` or Inter as display |
| ❌ FAIL | Small headlines | `text-xl` or `text-2xl` for section headlines |
| ✅ PASS | 3+ distinct sizes | label(sm) → headline(5xl+) → body(lg) → caption(sm) |
| ✅ PASS | Distinctive display font | `font-display` with Playfair/Fraunces/DM Serif |
| ✅ PASS | Dramatic headlines | `text-4xl md:text-5xl lg:text-6xl` or larger |

**Minimum Typography Scale:**
```
Section Headlines: text-4xl md:text-5xl lg:text-6xl (minimum)
Card Headlines: text-xl (minimum)
Body Text: text-base md:text-lg
```

#### 6.3 Spacing Check

| Status | Spacing | Context |
|--------|---------|---------|
| ❌ FAIL | `py-8` | Section padding |
| ❌ FAIL | `py-12` | Section padding |
| ⚠️ WARNING | `py-16` | Acceptable for small sections |
| ✅ PASS | `py-24` | Minimum section padding |
| ✅ PASS | `py-32` | Preferred section padding |
| ✅ PASS | `py-48` | Dramatic sections |

#### 6.4 Visual Elements Check

Count distinctive visual elements (need 3+ per section):

| Element | Example | Count? |
|---------|---------|--------|
| Background gradient | `bg-gradient-to-br from-primary/5 to-accent/5` | ✓ |
| Blur shapes | Decorative blur circles | ✓ |
| Image treatment | Shadow, glow, overlay | ✓ |
| Hover effects | Card lift, gradient border | ✓ |
| Animations | Staggered entrance | ✓ |
| Asymmetric layout | `grid-cols-[1fr,1.5fr]` | ✓ |
| Dramatic typography | 6xl+ with distinctive font | ✓ |
| Decorative elements | Shapes, lines, patterns | ✓ |

#### 6.5 Banned Pattern Check

Automatic FAIL if any of these are detected:

| Pattern | Detection |
|---------|-----------|
| Plain white centered | `bg-white` + `text-center` + no background treatment |
| Generic font stack | `Inter`, `Roboto`, `Arial` as primary display |
| Purple-blue gradient | `from-purple-*` `to-blue-*` on white background |
| Cookie-cutter cards | `grid-cols-3` with identical unstyled cards |
| Generic hero split | Text left, image right with no distinctive styling |
| Untreated images | `<Image className="rounded-lg">` only |

#### 6.6 Inspiration Alignment Check

| Question | Scoring |
|----------|---------|
| Does this section reference a specific inspiration? | Required |
| Are 3+ elements borrowed from that inspiration? | Required |
| Does the mood/atmosphere match? | Required |
| Would this fit on Dribbble/Behance? | Required |

### 7. SEO Validation (Per Section)

| Check | Requirement |
|-------|-------------|
| Heading Structure | Proper H1-H6 hierarchy |
| Image Alt Text | Descriptive, includes keywords where relevant |
| Semantic HTML | Use `<section>`, `<article>`, `<nav>` appropriately |
| Link Text | Descriptive, not "click here" |

## Validation Output Format

After validation, report:

```
## Validation Report: {SectionName}

### Technical Checks
| Check | Status | Issues |
|-------|--------|--------|
| TypeScript | ✓ | 0 errors |
| Design Tokens | ✓ | Using tokens |
| Responsive | ✓ | All breakpoints |
| Accessibility | ⚠️ | Missing alt on 1 image |
| Animation | ✓ | 60fps, GPU-accelerated |
| SEO | ✓ | H2 with keyword |

### Design Quality Checks (KRITISCH)
| Check | Status | Details |
|-------|--------|---------|
| Background | ✓ | Gradient mesh with blur shapes |
| Typography | ✓ | Fraunces display, text-6xl headline |
| Spacing | ✓ | py-32, generous whitespace |
| Visual Elements | ✓ | 4 distinctive elements found |
| Banned Patterns | ✓ | None detected |
| Inspiration Ref | ✓ | Matches bild1.png (Armonia) |

### Design Excellence Score
| Category | Score |
|----------|-------|
| Inspiration Alignment | 9/10 |
| Typography Distinction | 8/10 |
| Color Intentionality | 9/10 |
| Spatial Composition | 8/10 |
| Visual Details | 8/10 |
| Animation Strategy | 7/10 |
| Anti-Generic Check | 9/10 |
| **TOTAL** | **8.3/10** |

### 3 Distinctive Elements:
1. Warm cream background (#F2F0E9)
2. Asymmetric grid layout (1fr, 1.5fr)
3. Dramatic 7xl serif headline with blur glow image treatment

### Issues Found:
1. [A11Y] Image missing alt text at line 45

### Recommendations:
- Add descriptive alt text to hero image

### Status: ✓ PASS (Score: 8.3/10, Minimum: 7)
```

**IMPORTANT:** If Design Excellence Score < 7, the section FAILS regardless of technical checks!

## Status Levels

| Status | Meaning | Action |
|--------|---------|--------|
| ✓ Pass | All checks pass | Proceed |
| ⚠️ Warning | Minor issues | Fix before next section |
| ✗ Fail | Critical issues | Must fix immediately |

## Automated Checks

### TypeScript
```bash
npx tsc --noEmit
```

### ESLint
```bash
npx eslint . --ext .ts,.tsx
```

### Accessibility (via Playwright)
```typescript
import { injectAxe, checkA11y } from 'axe-playwright'

test('accessibility', async ({ page }) => {
  await page.goto('/')
  await injectAxe(page)
  await checkA11y(page)
})
```

### Lighthouse
```bash
npx lighthouse http://localhost:3000 --only-categories=accessibility,performance
```

## Integration with Workflow

This skill is used:
1. After `/implement` creates each section
2. Before moving to next page
3. During `/verify` final check

**Checkpoint Flow:**
```
Implement Section → Validate → Pass? → Next Section
                              ↓ Fail
                        Fix Issues → Re-validate
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deomiarn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
