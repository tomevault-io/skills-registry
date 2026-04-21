---
name: design-excellence
description: MANDATORY design quality validation for every section. Ensures implementations match inspiration quality and avoid generic AI aesthetics. Use during shape-pages and implement. Use when this capability is needed.
metadata:
  author: deomiarn
---

# Design Excellence

This skill enforces Dribbble-quality design standards by validating every section against the user's inspirations and the frontend-design philosophy.

**CRITICAL:** This skill is MANDATORY during:
- `/shape-pages` - before confirming each section design
- `/implement` - before marking any section complete
- `/validate` - as quality checkpoint
- `/refine` - after making changes

---

## The 7-Category Design Excellence Checklist

Every section MUST score >= 7/10 average to proceed. Score each category 0-10.

---

### Category 1: Inspiration Alignment (Weight: HIGH)

**Question:** Does this section explicitly reference and match the user's inspiration images?

| Score | Criteria |
|-------|----------|
| 0-3 | No inspiration reference, generic design |
| 4-6 | Vague reference, some elements match |
| 7-8 | Clear reference, most elements match inspiration |
| 9-10 | Perfect match to inspiration's essence and details |

**Required Evidence:**
- Name the specific inspiration file being referenced
- List 3+ elements borrowed from that inspiration
- Explain how the section captures the inspiration's mood

**FAIL Examples:**
- "I'll create a standard hero section" (no reference)
- "Generic card grid for features" (no inspiration alignment)

**PASS Examples:**
- "Based on Armonia Excursions (bild1.png): Split layout with large hero image on right, elegant serif headline, generous whitespace, warm beige background with dark green text"

---

### Category 2: Typography Distinction (Weight: HIGH)

**Question:** Does the typography stand out and avoid generic AI fonts?

| Score | Criteria |
|-------|----------|
| 0-3 | Using Inter, Roboto, Arial, system fonts as display |
| 4-6 | Acceptable fonts but flat hierarchy |
| 7-8 | Distinctive font with clear hierarchy |
| 9-10 | Memorable typography with dramatic hierarchy and character |

**BANNED Display Fonts:**
- Inter
- Roboto
- Arial
- System fonts (sans-serif, ui-sans-serif)
- Open Sans
- Lato (as display)

**Required Checks:**
| Check | Requirement |
|-------|-------------|
| Display Font | Distinctive, character-rich (Playfair, Fraunces, Space Grotesk, DM Serif, etc.) |
| Font Pairing | Clear contrast between display and body |
| Size Hierarchy | Dramatic jumps (h1: 4xl-7xl, not just 2xl) |
| Weight Contrast | Bold/Light used intentionally |

**FAIL Examples:**
```tsx
// BAD: Generic, flat
<h1 className="text-2xl font-semibold">Headline</h1>
<p className="text-base">Body text</p>
```

**PASS Examples:**
```tsx
// GOOD: Dramatic, distinctive
<h1 className="font-display text-5xl md:text-7xl font-bold tracking-tight leading-[1.1]">
  Headline <span className="text-primary">Accent</span>
</h1>
<p className="text-lg md:text-xl text-muted-foreground max-w-2xl">Body text</p>
```

---

### Category 3: Color Intentionality (Weight: HIGH)

**Question:** Are colors used with clear intention and hierarchy?

| Score | Criteria |
|-------|----------|
| 0-3 | Colors evenly distributed, no clear dominant |
| 4-6 | Some hierarchy but "safe" choices |
| 7-8 | Clear dominant + accent strategy |
| 9-10 | Masterful color usage matching inspiration mood |

**Required Checks:**
| Check | Requirement |
|-------|-------------|
| Dominant Color | One clear primary that dominates (60-70% of color) |
| Accent Sparingly | Accent used only for emphasis (10-20%) |
| Not Evenly Distributed | Colors have clear hierarchy |
| Matches Inspiration | Palette derived from user's inspirations |

**BANNED Patterns:**
- Purple/Blue gradient on white background (AI cliche)
- Generic blue (#0066FF) primary
- Every element has a different color
- "Safe" neutral-only palettes with no personality

**FAIL Examples:**
```tsx
// BAD: Generic blue, no hierarchy
<Button className="bg-blue-600">Click</Button>
<Card className="bg-blue-50">...</Card>
<Badge className="bg-blue-100">Tag</Badge>
```

**PASS Examples:**
```tsx
// GOOD: Dominant background, strategic accent
<section className="bg-[#F2F0E9]"> {/* Dominant warm neutral */}
  <h2 className="text-[#2D3B2D]">...</h2> {/* High contrast text */}
  <Button className="bg-[#2D3B2D] text-white">...</Button> {/* Accent for CTA only */}
</section>
```

---

### Category 4: Spatial Composition (Weight: HIGH)

**Question:** Does the layout use space intentionally with proper breathing room?

| Score | Criteria |
|-------|----------|
| 0-3 | Everything centered, equal spacing, cramped |
| 4-6 | Some whitespace but predictable layout |
| 7-8 | Generous whitespace, intentional asymmetry |
| 9-10 | Masterful spatial composition with visual flow |

**Required Checks:**
| Check | Requirement |
|-------|-------------|
| Section Padding | `py-24` minimum (96px), prefer `py-32` (128px) |
| Asymmetry OR Intentional Symmetry | Not accidental centering |
| Visual Flow | Eye moves naturally through content |
| Layering/Overlap | Depth through z-index, negative margins, or overlapping elements |
| Max-Width Constraints | Content doesn't stretch full-width unnecessarily |

**FAIL Examples:**
```tsx
// BAD: Cramped, generic
<section className="py-8">
  <div className="container mx-auto text-center">
    <h2>Title</h2>
    <p>Description</p>
    <div className="grid grid-cols-3 gap-4">
```

**PASS Examples:**
```tsx
// GOOD: Generous, intentional
<section className="py-32 lg:py-48">
  <Container>
    <div className="max-w-4xl"> {/* Intentional constraint */}
      <div className="grid lg:grid-cols-[1fr,1.5fr] gap-16 items-center">
        {/* Asymmetric grid */}
```

---

### Category 5: Visual Details & Atmosphere (Weight: MEDIUM)

**Question:** Does the section have atmosphere, depth, and refined details?

| Score | Criteria |
|-------|----------|
| 0-3 | Plain solid backgrounds, no depth |
| 4-6 | Some styling but feels flat |
| 7-8 | Clear atmosphere with deliberate details |
| 9-10 | Rich, immersive visual experience |

**Required One of These Background Treatments:**
| Treatment | Code Example |
|-----------|--------------|
| Gradient Mesh | `bg-gradient-to-br from-primary/5 via-background to-accent/5` |
| Subtle Noise/Texture | `bg-noise opacity-[0.02]` |
| Geometric Pattern | Pattern overlay with low opacity |
| Blurred Shapes | `blur-3xl rounded-full absolute` background shapes |
| Image with Overlay | `bg-cover` with gradient overlay |
| Intentional Solid | Must be specific color from inspiration, not default white |

**Required Visual Details:**
| Detail | Examples |
|--------|----------|
| Image Treatment | Shadows, overlays, blur effects, borders |
| Card Styling | Backdrop blur, gradient borders, hover glow |
| Decorative Elements | Subtle shapes, lines, dots, patterns |
| Shadow Depth | Layered shadows or intentional flat design |

**FAIL Examples:**
```tsx
// BAD: Plain, no atmosphere
<section className="bg-white">
  <Card className="border rounded-lg p-4">
```

**PASS Examples:**
```tsx
// GOOD: Atmospheric, detailed
<section className="relative py-32 overflow-hidden">
  <div className="absolute inset-0 bg-gradient-to-br from-primary/5 via-background to-accent/5" />
  <div className="absolute top-20 -right-20 w-96 h-96 bg-primary/10 rounded-full blur-3xl" />

  <Card className="relative bg-card/50 backdrop-blur-sm border-0 shadow-xl">
    <div className="absolute -inset-0.5 bg-gradient-to-r from-primary/20 to-accent/20 rounded-xl opacity-0 group-hover:opacity-100 blur transition" />
```

---

### Category 6: Animation Strategy (Weight: MEDIUM)

**Question:** Do animations enhance the experience without overwhelming?

| Score | Criteria |
|-------|----------|
| 0-3 | No animations or too many competing animations |
| 4-6 | Basic fade-in only |
| 7-8 | Thoughtful entrance + hover animations |
| 9-10 | Choreographed animation system with scroll triggers |

**Required Animation Elements:**
| Element | Animation Type |
|---------|---------------|
| Section Entrance | Fade + slide, staggered children |
| Interactive Elements | Hover scale, color transitions |
| Scroll Triggers | Viewport entry animations |
| Micro-interactions | Button press, card hover lift |

**Animation Rules:**
- Only animate `transform` and `opacity` (GPU-accelerated)
- Stagger children: `staggerChildren: 0.1` minimum
- Respect `prefers-reduced-motion`
- Duration: 300-600ms for entrances, 150-200ms for hovers

**FAIL Examples:**
```tsx
// BAD: No animation
<div>
  <h2>Static content</h2>
</div>
```

**PASS Examples:**
```tsx
// GOOD: Choreographed animation
const containerVariants = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: { staggerChildren: 0.15, delayChildren: 0.1 }
  }
}

const itemVariants = {
  hidden: { opacity: 0, y: 20 },
  visible: {
    opacity: 1,
    y: 0,
    transition: { duration: 0.5, ease: [0.4, 0, 0.2, 1] }
  }
}

<motion.div
  variants={containerVariants}
  initial="hidden"
  whileInView="visible"
  viewport={{ once: true, margin: "-100px" }}
>
```

---

### Category 7: Anti-Generic Check (Weight: CRITICAL)

**Question:** Would this design be memorable on Dribbble/Behance?

| Score | Criteria |
|-------|----------|
| 0-3 | Looks like generic AI output or template |
| 4-6 | Acceptable but forgettable |
| 7-8 | Has distinctive elements, stands out |
| 9-10 | Truly memorable, portfolio-worthy |

**BANNED Patterns (Automatic Fail if Present):**
| Pattern | Description |
|---------|-------------|
| Plain White Centered | `bg-white` with centered content, no treatment |
| Generic Font Stack | Inter/Roboto/Arial as display font |
| Purple-Blue Gradient | Overused AI aesthetic, especially on white |
| Cookie-Cutter Cards | Equal-sized cards in perfect grid |
| Generic Hero Split | Left text, right image without styling |
| Stock Photo Look | Untreated placeholder images |
| Safe Color Choices | Pure blue (#0066FF), pure green (#00FF00) |

**Required: 3+ Distinctive Elements**

Every section must have at least 3 elements that would make it memorable:

Examples of Distinctive Elements:
- Unique background treatment (gradient mesh, noise, shapes)
- Dramatic typography hierarchy
- Asymmetric layout
- Creative image treatment (overlapping, masked, styled)
- Distinctive color palette from inspiration
- Choreographed animations
- Creative hover states
- Decorative elements (shapes, lines, patterns)
- Overlapping/layered composition
- Creative use of negative space

---

## Scoring and Validation

### Calculate Score

```
Total Score = Average of all 7 categories

Categories:
1. Inspiration Alignment: _/10
2. Typography Distinction: _/10
3. Color Intentionality: _/10
4. Spatial Composition: _/10
5. Visual Details: _/10
6. Animation Strategy: _/10
7. Anti-Generic Check: _/10

Total: _/10
```

### Validation Thresholds

| Score | Status | Action |
|-------|--------|--------|
| 9-10 | EXCELLENT | Proceed - Portfolio quality |
| 7-8 | PASS | Proceed - Good quality |
| 5-6 | WARNING | Can proceed with noted improvements |
| 0-4 | FAIL | MUST REDESIGN before proceeding |

---

## Validation Output Format

After scoring, output:

```markdown
## Design Excellence Report: {SectionName}

**Inspiration Reference:** {inspiration-file}

| Category | Score | Notes |
|----------|-------|-------|
| Inspiration Alignment | ?/10 | {specific notes} |
| Typography Distinction | ?/10 | {font used, hierarchy} |
| Color Intentionality | ?/10 | {palette strategy} |
| Spatial Composition | ?/10 | {padding, layout} |
| Visual Details | ?/10 | {atmosphere, effects} |
| Animation Strategy | ?/10 | {animation types} |
| Anti-Generic Check | ?/10 | {distinctive elements} |

**Total Score: ?/10**

**3 Distinctive Elements:**
1. {element 1}
2. {element 2}
3. {element 3}

**Status: {PASS/FAIL/WARNING}**

{If FAIL or WARNING:}
**Issues to Fix:**
- {issue 1}
- {issue 2}

**Suggested Improvements:**
- {improvement 1}
- {improvement 2}
```

---

## Integration Points

### In `/shape-pages`

After designing each section, run Design Excellence Check:
- Score all 7 categories
- BLOCK if score < 7
- Force redesign before continuing

### In `/implement`

Before completing each section:
- Verify implementation matches design
- Score implementation against checklist
- BLOCK if score < 7

### In `/validate`

Include Design Excellence as validation category:
- Review all sections
- Aggregate scores
- Flag any section below threshold

---

## Quick Reference: What Makes Design Excellent

### DO (Dribbble-Quality):
- Reference specific inspiration images
- Use distinctive display fonts (Playfair, Fraunces, DM Serif, etc.)
- Create dominant + accent color strategy
- Use generous whitespace (py-32+)
- Add atmospheric backgrounds
- Choreograph entrance animations
- Include 3+ memorable elements per section

### DON'T (Generic AI Output):
- Design without inspiration reference
- Use Inter/Roboto/Arial for headlines
- Distribute colors evenly
- Use tight spacing (py-8, py-12)
- Leave plain white/gray backgrounds
- Skip animations or overdo them
- Create forgettable, template-like sections

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deomiarn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
