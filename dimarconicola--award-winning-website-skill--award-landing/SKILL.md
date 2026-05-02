---
name: award-landing
description: Generate Awwwards-quality landing pages. Use when creating marketing sites, redesigning with premium aesthetics, or when user mentions "award-winning design. Use when this capability is needed.
metadata:
  author: dimarconicola
---

# Award-Winning Landing Page Director

Transform briefs into sites that win Awwwards Site of the Day.

**What wins awards:** A singular concept executed relentlessly. Not "many things done well" — ONE thing done unforgettably.

---

## Brief Intake

Before starting, ensure you have these. Ask if missing:

| Required | Why |
|----------|-----|
| Brand name | Identity foundation |
| Industry/product type | Cluster selection |
| Target audience | Tone calibration |
| 3 adjectives | Vibe direction |
| Competitors to avoid | Differentiation |
| Content scope | Section planning |

| Helpful If Provided | Why |
|---------------------|-----|
| Existing brand colors | Palette constraint |
| Typography preferences | Type direction |
| Reference sites they like | Style signal |
| Hero imagery/video | Asset planning |
| Key differentiator | Concept seed |
| Launch timeline | Complexity calibration |

**If user provides a minimal brief:** Extract what you can, then ask 2-3 targeted questions before proceeding.

---

## The 4-Phase Process

### Phase 1: Concept Extraction
Before any visuals, answer these:

1. **What's the single truth about this brand?** Extract ONE essential quality.
2. **What metaphor embodies it?** (Precision → watchmaking. Speed → wind. Trust → architecture.)
3. **What feeling persists after the user leaves?** (Awed? Reassured? Delighted?)
4. **What's the Signature Moment?** One interaction users will screenshot and share.

> See [concepts/THESIS.md](concepts/THESIS.md) for the full framework.

### Phase 2: Vocabulary Selection
Constrained choices from the design vocabulary:

| Decision | Options | Reference |
|----------|---------|-----------|
| Primary Vibe | cinematic, editorial, playful, brutal, minimal, luxury, futuristic, artisanal, experimental, bold, ethereal | [reference/VIBES.md](reference/VIBES.md) |
| Secondary Vibe | Creates tension with primary | Same |
| Motion Regime | subtle, kinetic-type, scroll-scene, immersive, parallax, cursor-led, minimal-motion | [reference/VIBES.md](reference/VIBES.md) |
| Materiality | glass, paper, metal, fabric, neon, organic | Same |
| Style Cluster | Match vibe combination to proven pattern | [reference/CLUSTERS.md](reference/CLUSTERS.md) |
| Reference DNA | 2-3 sites to borrow specific elements from | [reference/CORPUS.md](reference/CORPUS.md) |

### Phase 3: Hierarchy Decisions
Decide in this order — earlier decisions constrain later ones:

1. **Typography** — 80% of visual impact. The typeface IS the design.
2. **Layout Rhythm** — Density vs. whitespace. Vertical vs. horizontal flow.
3. **Color Restraint** — 2-3 colors max. One accent with meaning.
4. **Motion Philosophy** — Slower = luxury. Snappy = efficient. Purposeful always.
5. **Signature Moment** — The ONE thing they'll remember.

> See [taste/TYPOGRAPHY.md](taste/TYPOGRAPHY.md) for type-first thinking.

### Phase 4: Self-Evaluation
Before generating code, pass these tests:

- [ ] Can I describe the concept in 5 words?
- [ ] Is there ONE moment users will screenshot?
- [ ] Have I been restrained enough that peaks feel earned?
- [ ] Would every element survive the question "does this serve the concept?"
- [ ] Would a jury notice care in the hover states?

> See [taste/EVALUATION.md](taste/EVALUATION.md) for the full rubric.

---

## What Separates SOTD from "Good"

### 1. Singular Concept, Relentless Execution
Winners don't do many things well — they do ONE thing unforgettably.
- Igloo Inc: seamless shared-element transitions
- Bruno Simon: a 3D car you drive
- Lusion: fluid mouse distortion

The concept should be explainable in 5 words and visible in every interaction.

### 2. Narrative Choreography
Motion isn't "adding animations." It's directing the eye through a story with cinematic timing: hold, build, payoff. Elements enter in relationship to each other, not independently.

### 3. The Signature Moment™
Every SOTY has ONE moment users remember and share:
- Ponpon Mania: physics candy you throw
- Cartier: light ray product reveal
- Slosh Seltzer: liquid physics simulation

This is the "screenshot moment" that gets posted to design communities.

### 4. Tension Between Restraint and Surprise
Winners are brutally restrained (heavy whitespace, limited color) punctuated by controlled excess. The contrast creates drama. Restraint makes surprises land harder.

### 5. Typography as Primary Material
Type isn't decoration — it's architecture. Winners use massive display headlines, custom variable font animations, type that IS the hero rather than describing one.

### 6. Obsessive Micro-Details
Jury members browse at 100% zoom and notice the hover state on a 12px icon. Custom cursor behavior, tactile button hovers, designed focus rings — every state is intentional.

### 7. Speed as Luxury
The fastest sites feel expensive. LCP < 1.5s, instant interactions, 60fps scroll. Loading should be branded. Slow shatters the illusion.

---

## Motion Patterns

> See [reference/MOTION.md](reference/MOTION.md) for the full pattern vocabulary (Smooth Scroll, Split Text, Scroll Pin, Parallax, Magnetic Hover, Clip-Path Reveal, etc.).
>
> Always respect `prefers-reduced-motion`. Replace with instant states.

---

## Anti-Patterns (What Loses)

| Anti-Pattern | Why It Fails |
|--------------|--------------|
| Generic SaaS Template | Hero + 3 features + pricing = forgettable |
| Motion Without Purpose | Animations must guide attention or provide feedback |
| Stock Photo Overload | Max 3-5 stock images; prefer custom/3D/illustration |
| Typography Neglect | Type is 80% of design — generic fonts = generic site |
| Scroll Hijacking | Scrub is fine; full takeover breaks trust |
| Ignoring Performance | LCP > 2.5s = immediate disqualification |
| Complexity Without Concept | Impressive tech with no soul = "cool demo, not a site" |
| Restraint Without Payoff | All quiet, no peaks = boring |

---

## Performance Philosophy

Speed is luxury. Slow shatters the illusion.

**Targets:**
- LCP < 1.5s (ideally < 1s)
- FID < 100ms
- CLS < 0.1
- 60fps scroll always

**How to achieve it:**
| Technique | When |
|-----------|------|
| next/image with priority | Hero images |
| Lazy load below fold | All non-hero images |
| Dynamic import GSAP | Don't block initial render |
| Preload fonts | Display fonts in hero |
| Video poster + lazy | Hero video |
| Reduce ScrollTrigger count | Combine where possible |
| will-change sparingly | Only on animated elements |

**Tradeoffs when needed:**
| If... | Sacrifice... |
|-------|-------------|
| Hero video kills LCP | Use image sequence or poster until interaction |
| Too many scroll triggers | Combine sections, reduce parallax layers |
| Custom fonts slow | Use system font stack for body, custom for display only |
| WebGL/3D needed | Lazy load, show fallback first |

**Loading as branding:** If load is unavoidable, brand it. Animated logo, progress bar with personality.

### Performance Validation Loop

After generating code, run this loop:

1. Build production: `npm run build`
2. Audit: `npx lighthouse http://localhost:3000 --view --preset=desktop`
3. Check:
   - LCP < 1.5s? If not → optimize hero assets, preload fonts
   - CLS < 0.1? If not → set explicit dimensions on images/embeds
   - 60fps scroll? If not → reduce ScrollTrigger count, audit `will-change`
4. Fix issues and **repeat from step 2** until all targets pass
5. Only then consider the build complete

---

## Assets & Content

**When user provides assets:** Use them. Optimize aggressively.

**When assets are missing:**
| Asset Type | Strategy |
|------------|----------|
| Hero image/video | Use placeholder with clear TODO comment |
| Product shots | Geometric shapes or branded gradients as placeholders |
| Icons | Lucide React (consistent, crisp) |
| Illustrations | Suggest style, use colored shapes as stand-in |
| Logos (client logos) | Gray rectangles with "LOGO" text |

**Stock photo rules:**
- Maximum 3-5 stock images total
- Never in hero (hero must be custom or product)
- Prefer abstract/textural over people
- Consistent treatment (all B&W, all duotone, etc.)

**Illustration vs. photography:**
| Vibe | Prefer |
|------|--------|
| playful, artisanal | Illustration |
| luxury, cinematic, editorial | Photography |
| minimal, brutal | Neither — geometry, type, negative space |
| futuristic | 3D renders or abstract gradients |

---

## Output Structure

Generate artifacts in chat, then code to disk:

### 1. DIRECTION.md (show in chat)
- **Design Thesis** — One sentence capturing the essence
- **Mood Triad** — 3 adjectives with manifestation
- **Style Classification** — Vibe, cluster, motion regime, materiality
- **Color Philosophy** — Palette with rationale
- **Typography System** — Fonts with roles
- **Reference DNA** — 2-3 sites, what to borrow

### 2. PAGE_PLAN.md (show in chat)
- **Section Overview** — Purpose and layout of each section
- **Signature Moment** — Where and what
- **Responsive Strategy** — See below

### 3. Code (write to disk)
Next.js 14+ App Router, Tailwind CSS, GSAP + ScrollTrigger, TypeScript.

---

## File Structure

Organize generated code consistently:

```
app/
├── layout.tsx              # Root layout, fonts, metadata
├── page.tsx                # Home page, imports sections
├── globals.css             # Tailwind + custom properties
├── components/
│   ├── sections/           # One file per page section
│   │   ├── Hero.tsx
│   │   ├── Features.tsx
│   │   └── ...
│   ├── ui/                 # Reusable primitives
│   │   ├── Button.tsx
│   │   ├── Magnetic.tsx    # Magnetic hover wrapper
│   │   └── ...
│   └── motion/             # Animation utilities
│       ├── SplitText.tsx   # Text splitting component
│       ├── ScrollPin.tsx   # Scroll-pinned section
│       ├── Reveal.tsx      # Intersection-based reveal
│       └── SmoothScroll.tsx # Lenis wrapper
├── hooks/
│   ├── useGSAP.ts          # GSAP context hook
│   └── useReducedMotion.ts # prefers-reduced-motion
└── lib/
    └── utils.ts            # cn(), animation helpers
```

**Section components:** Each section is self-contained with its own animations. Initialize GSAP in `useLayoutEffect` with proper cleanup.

**Shared motion:** Common patterns (Magnetic, Reveal, SplitText) live in `components/motion/` for reuse.

---

## Responsive Strategy

| Breakpoint | Approach |
|------------|----------|
| Mobile (<768px) | Simplified: reduce motion, stack layouts, touch-friendly |
| Tablet (768-1024px) | Hybrid: some motion, adapted layouts |
| Desktop (>1024px) | Full experience |

**What to simplify on mobile:**
- Horizontal scroll → Vertical stack or swiper
- Parallax → Static or subtle
- Custom cursor → Remove (no cursor on touch)
- Scroll pin → Shorter or removed
- Split text → Simpler word-level, not char-level

**Touch substitutes:**
| Desktop | Mobile |
|---------|--------|
| Hover states | Tap states or remove |
| Magnetic hover | Remove |
| Cursor-led motion | Scroll-driven instead |

**Mobile-first implementation:** Write base styles for mobile, use `md:` and `lg:` for enhancements.

---

## Quick Reference

> See [reference/CHEATSHEET.md](reference/CHEATSHEET.md) for Vibes→Clusters mapping, Type Pairings, and Color Mode guidance.

---

## Supporting Files

| File | Purpose |
|------|---------|
| [concepts/THESIS.md](concepts/THESIS.md) | Developing singular concept from brief |
| [concepts/SIGNATURE.md](concepts/SIGNATURE.md) | Designing the memorable moment |
| [concepts/TENSION.md](concepts/TENSION.md) | Restraint and surprise principles |
| [taste/TYPOGRAPHY.md](taste/TYPOGRAPHY.md) | Type-first design thinking |
| [taste/EVALUATION.md](taste/EVALUATION.md) | Self-critique rubric |
| [reference/VIBES.md](reference/VIBES.md) | Vibe and motion vocabulary |
| [reference/CLUSTERS.md](reference/CLUSTERS.md) | Style clusters with exemplars |
| [reference/CORPUS.md](reference/CORPUS.md) | 30+ Awwwards winners with analysis |
| [reference/MOTION.md](reference/MOTION.md) | Motion pattern vocabulary |
| [reference/CHEATSHEET.md](reference/CHEATSHEET.md) | Vibes→Clusters, Type Pairings, Color Mode |
| [examples/MAISON.md](examples/MAISON.md) | Worked example: luxury watch brand |
| [examples/FLUX.md](examples/FLUX.md) | Worked example: developer tool |
| [examples/BOUNCE.md](examples/BOUNCE.md) | Worked example: playful consumer app |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dimarconicola) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
