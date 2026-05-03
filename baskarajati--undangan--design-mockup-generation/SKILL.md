---
name: design-mockup-generation
description: Framework for generating strategic design mockups using AI image generation with properly structured prompts Use when this capability is needed.
metadata:
  author: baskarajati
---

# Design Mockup Generation Skill

## Purpose

Provides a systematic framework for generating high-quality design mockups that accurately represent strategic design directions, ensuring mockups are useful for decision-making rather than just pretty pictures.

## Inputs

- Strategic direction brief (palette, typography, signature elements)
- Product context and target audience
- Sections required (hero, features, etc.)

## Outputs

- Mockup images stored under `.agent/artifacts/{conversation-id}/`
- Prompts used for each mockup
- Comparison notes or carousel-ready output

## Prerequisites

- Approved strategic direction(s) or comparison requirement
- Ability to generate or source mockups (tool or manual)

## Tools & Availability

- `generate_image` (if unavailable, document prompt and create in Figma or skip with rationale)

## When to Use

- Need visual representation of design concepts
- Comparing multiple design directions
- Presenting strategic options to stakeholders
- Validating design direction before implementation
- Creating design system references

## Core Principles

### 1. Mockups Must Serve Strategy

**Bad mockup:** Generic pretty design  
**Good mockup:** Specific representation of a strategic design direction with clear differentiators

### 2. Details Drive Differentiation

**Vague prompt:** "A modern, clean landing page"  
**Specific prompt:** "Deep emerald (#1B4332) background, Freight Display serif at 72px, hand-drawn gold arrows"

### 3. Generate for Comparison

Always generate **minimum 3 distinct options**, not 3 variations of the same idea.

## Mockup Prompt Structure

### Template

```
A [product type] [section type] mockup in "[Design Direction Name]" style.

[FOUNDATION - 40% of prompt]
Design elements:
- Color: [Specific palette with hex codes]
- Typography: [Font names, weights, sizes]
- Layout: [Composition description]

[SIGNATURE - 30% of prompt]  
- [Unique visual element that defines this direction]
- [Texture/depth treatment]
- [Distinctive pattern]

[EMOTION - 20% of prompt]
Mood: [3-5 specific adjectives]
Feeling: [What user should feel when seeing this]

[CONSTRAINTS - 10% of prompt]
[What NOT to include, what to avoid]
```

### Example: High-End Editorial

```
A premium wedding invitation landing page hero section mockup in a "High-End Editorial" style.

Design elements:
- Deep emerald green (#1B4332) and champagne/cream (#F5E6D3) color palette
- Large elegant editorial serif typography for headline "Your Wedding, Effortlessly Beautiful"
- Subtle paper grain texture on cream background
- Refined phone mockup showing a wedding invitation with elegant typography
- Clean, magazine-quality layout with generous whitespace
- Editorial New or Freight Display style fonts
- Subtle gold accent elements

The design should feel like a luxury wedding magazine (Vogue Weddings), not a SaaS tool. Premium, sophisticated, timeless.

DO NOT include: bright colors, playful elements, casual fonts, gradients.
```

## Strategic Direction Templates

### 1. High-End Editorial/Luxury

**Use when:** Premium positioning, timeless aesthetic, conservative market

```
[Product type] in "High-End Editorial" style.

Color:
- Deep [rich color] (#HEX) primary
- Champagne/cream (#HEX) background  
- Gold (#HEX) subtle accents

Typography:
- Editorial serif (Freight Display / Playfair / Cormorant)
- Large headlines (60-80px)
- Generous line height (1.4-1.6)

Signature:
- Magazine spread composition
- Paper grain texture
- Professional photography integration
- Numbered sections or lettered chapters

Mood: Sophisticated, timeless, established, trustworthy, premium

Vibe: [Publication] meets [tech product]
Example: Vogue meets Apple, Monocle meets Notion
```

### 2. Human Touch/Warm

**Use when:** Emotional connection priority, approachable, friendly

```
[Product type] in "The Human Touch" style.

Color:
- Warm cream/off-white (#FDF9F3) base
- Terracotta/coral (#D85E4E) primary
- Soft sage/blue accents

Typography:
- Friendly slab serif or rounded sans (DM Serif / Outfit)
- Handwritten script (Caveat / Homemade Apple) for annotations
- Mix of weights for personality

Signature:
- Organic blob shapes instead of rectangles
- Hand-drawn arrows, stars, underlines
- Speech bubble frames
- Collage-style overlapping elements
- Real human photography in organic frames

Mood: Warm, friendly, approachable, honest, empathetic

Vibe: Scrapbook meets product page, handcrafted with love
Should feel: Made by humans for humans, not a corporate machine
```

### 3. Tech Brutalist/Bold

**Use when:** Maximum differentiation, AI/tech brand, modern edge

```
[Product type] in "Tech Luxury Brutalist" style.

Color:
- High contrast: Black/off-white with single neon accent
- Electric [color] (#E31745) for CTAs and highlights
- Minimal palette for maximum impact

Typography:
- Massive sans-serif (Inter/SF Pro) at 700-900 weight
- Headlines that break containers
- Tight leading, bold tracking

Signature:
- Thick borders (3-5px)
- Circular badge/sticker elements
- Glassmorphism cards (blur + transparency)
- Grid overlay texture
- High visual weight across all elements

Mood: Confident, bold, disruptive, unapologetic, modern

Vibe: Apple meets Parallel, tech luxury
Should feel: Premium tech product, not apologetically "nice"
```

### 4. Hybrid Combinations

**Most projects need hybrids:**

```
[Product type] in "Hybrid [A] + [B]" style.

FOUNDATION (70%):
[Elements from conservative/safe direction]

SOUL (30%):
[Elements from emotional/bold direction that add character]

Balance: [Percentage] [adjective] + [percentage] [adjective]

Example:
Balance: 70% Editorial polish + 30% Human playfulness
```

## Variant Generation

### Light vs Dark Variants

**Always generate both for hybrid recommendations:**

**Light Variant:**
- Cream/white background
- Dark text
- Best for: Main landing page, daytime vibe, approachable

**Dark Variant:**
- Dark/rich background  
- Light text
- Best for: Premium sections, evening vibe, dramatic impact

### Section-Specific Mockups

After strategic direction chosen, generate:

1. **Hero Section** (always first)
2. **Features/Benefits** (grid or cards)
3. **Testimonials** (social proof treatment)
4. **CTA Section** (conversion-focused)
5. **Footer** (completeness check)

## Quality Checklist

Before requesting mockup generation:

- [ ] Specific hex codes included (not just "green")
- [ ] Font names specified (not just "serif")
- [ ] Unique signature element described
- [ ] Mood adjectives are specific (not "modern")
- [ ] Clear differentiation from other options
- [ ] Context-appropriate (wedding ≠ SaaS ≠ ecommerce)
- [ ] Constraints listed (what NOT to include)

## After Generation

### 1. Verification

Check that mockup actually represents the direction:
- ✅ Colors match specified palette
- ✅ Typography feels appropriate
- ✅ Signature element is present
- ✅ Mood matches adjectives

If mockup doesn't match: **regenerate with more specific prompt**, don't settle.

### 2. Comparison Documentation

Create structured comparison:

````markdown
## Option A: [Name]

![Mockup](/path/to/mockup.png)

### Design DNA
| Element | Specification |
|---------|---------------|
| Color | [Palette] |
| Typography | [Fonts] |
| Signature | [Unique element] |

### Pros
- ⭐⭐⭐⭐⭐ [Criterion]: [Why]

### Cons  
- ❌ [Risk]: [Mitigation]
````

### 3. User Presentation

Present as a **carousel in markdown** for easy comparison:

````markdown
````carousel
![Option A: Editorial](/path/a.png)
<!-- slide -->
![Option B: Human](/path/b.png)
<!-- slide -->
![Option C: Brutalist](/path/c.png)
````
````

## Common Mistakes

### ❌ Vague Prompts

**Bad:**
```
A modern, clean landing page with good design
```

**Good:**
```
Deep emerald (#1B4332) background, Freight Display serif at 72px, 
hand-drawn gold arrows, cream (#F5E6D3) phone mockup, 
paper grain texture, magazine spread layout
```

### ❌ Missing Context

**Bad:**
```
A beautiful hero section
```

**Good:**
```
A wedding invitation platform hero section that needs to feel 
PREMIUM (not cheap) while maintaining WARMTH (not cold corporate)
```

### ❌ Not Differentiating Options

**Bad:**
```
Option A: Modern design with blue
Option B: Modern design with green  
Option C: Modern design with purple
```

**Good:**
```
Option A: Editorial serif, magazine layout, conservative
Option B: Handwritten accents, organic blobs, playful
Option C: Brutalist massive type, thick borders, disruptive
```

## Tool Usage

### generate_image

```typescript
generate_image({
  ImageName: "option_a_editorial",  // descriptive, lowercase_underscore
  Prompt: `[Detailed structured prompt following template]`
})
```

### Naming Convention

```
{direction}_{variant}_{section}.png

Examples:
- hybrid_light_hero.png
- option_a_editorial.png  
- brutalist_dark_features.png
```

## Integration

Use with:
- **Brand Identity Research** skill (strategic options development)
- **Design Direction Decision** workflow
- Design system documentation
- A/B testing visual concepts

## Iteration Process

1. Generate initial mockup
2. Verify against strategic direction
3. If mismatch: refine prompt with MORE specificity
4. If match: generate variants (dark mode, different sections)
5. Present 2-3 options minimum for comparison
6. Request user selection
7. Generate section-specific mockups for chosen direction

## Success Criteria

✅ Mockup clearly represents strategic direction  
✅ Visual differentiators are obvious  
✅ User can make informed decision from mockups  
✅ Mockup is implementable (not fantasy design)  
✅ Details support the strategic narrative  

## Verification

- [ ] Mockups saved under `.agent/artifacts/{conversation-id}/` with descriptive names
- [ ] Prompt includes hex codes, font names, signature element, and constraints
- [ ] At least 3 distinct options or 2 variants for hybrid recommendation
- [ ] Each mockup validated against palette + typography intent

**Pass/Fail:** Pass only if all checks above are satisfied.

## Risks & Mitigations

- Over-polished but off-strategy visuals → verify against prompt criteria
- Tool unavailability → document prompts and deliver manual mockups
- Homogeneous options → enforce distinct strategic directions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baskarajati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
