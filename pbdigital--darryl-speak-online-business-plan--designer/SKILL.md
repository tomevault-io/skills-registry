---
name: frontend-design
description: Create distinctive landing pages and marketing materials with high design quality. Use ONLY when explicitly asked for marketing pages, landing pages, hero sections, or promotional content. Do NOT use for dashboards, forms, admin interfaces, or application UI components. Use when this capability is needed.
metadata:
  author: pbdigital
---

# Frontend Design Skill

Create distinctive landing pages and marketing materials with exceptional visual impact and high design quality. Use this skill ONLY when explicitly asked to build marketing pages, landing pages, hero sections, or promotional content. Do NOT use for dashboards, forms, admin interfaces, or application UI — those should follow the project's established design system in CLAUDE.md.

---

## 1. Context & Goals

Before anything else, understand what you're building:

**Purpose**
- What problem does this interface solve?
- What's the primary user action? (There should be ONE clear answer)
- What does success look like for the user?

**Audience**
- Who uses this? (Demographics, technical sophistication, context of use)
- What device/environment? (Desktop at work, mobile on the go, tablet on couch)
- What's their emotional state when arriving? (Frustrated seeking support, excited browsing, focused working)

**Constraints**
- Technical requirements (framework, browser support, performance budget)
- Brand guidelines or existing design system to honor
- Accessibility requirements (WCAG level, specific accommodations)

**Context-Specific Approach**

Different interfaces demand different design priorities:

| Context | Priority | Aesthetic Range |
|---------|----------|-----------------|
| Marketing/Landing | Emotional impact, storytelling, clear CTAs | High creativity, bold choices |
| Dashboard/App | Information density, scannability, efficiency | Restrained, consistent patterns |
| E-commerce | Trust, clarity, frictionless flow | Product-focused, conversion-optimized |
| Content/Editorial | Reading experience, typography | Typography-first, minimal distraction |
| Tools/Utilities | Speed, keyboard support, power-user patterns | Functional, low friction |

Match aesthetic ambition to context. A brutalist marketing page works; a brutalist accounting dashboard doesn't.

---

## 2. UX Foundations

Establish these BEFORE visual design:

### Information Hierarchy

What do users need to see first, second, third? Rank every element:

1. **Primary**: The ONE thing (main CTA, key metric, hero message)
2. **Secondary**: Supporting context (subheadlines, key features, navigation)
3. **Tertiary**: Details on demand (fine print, secondary actions, metadata)

If everything is important, nothing is. Be ruthless.

### User Flows

Map the critical path:
- Entry point → Primary action → Success state
- What's the minimum number of steps?
- Where might users get stuck or confused?
- What are the escape hatches if they're in the wrong place?

### Component State Coverage

**Every interactive component needs ALL states designed:**

| State | Purpose | Example |
|-------|---------|---------|
| Default | Resting, available | Button ready to click |
| Hover | Indicates interactivity | Subtle lift, color shift |
| Focus | Keyboard navigation (REQUIRED) | Visible ring/outline |
| Active/Pressed | Confirms input received | Depress, color change |
| Disabled | Unavailable, explains why | Grayed, cursor: not-allowed |
| Loading | Action in progress | Spinner, skeleton, progress |
| Error | What went wrong + how to fix | Red border, inline message |
| Success | Confirmation + next step | Check mark, transition out |
| Empty | No data yet, guide user | Illustration, prompt to act |

Never ship a component with only the happy path designed.

### Interaction Feedback

Every user action needs acknowledgment:
- Click → Immediate visual response (< 100ms)
- Form submit → Loading state → Success/error
- Navigation → Clear indication of current location
- Hover → Subtle confirmation element is interactive

Silence is confusing. Always respond.

---

## 3. Design Direction

Now commit to a BOLD aesthetic direction.

### Finding References

Before designing, gather inspiration:
- **Awwwards, Dribbble, Behance**: Contemporary trends
- **Brutalist Websites, Hoverstat.es**: Unconventional approaches
- **Real-world**: Print design, architecture, fashion, nature, album covers

Collect 3-5 references that capture the intended mood. Articulate WHY each works—what specific quality to borrow.

### Choosing a Tone

Pick a direction and commit fully. Half-measures create forgettable work.

**Aesthetic Spectrums** (pick a position on each):

```
Minimal ←————————————————→ Maximalist
Warm    ←————————————————→ Cool
Organic ←————————————————→ Geometric
Playful ←————————————————→ Serious
Retro   ←————————————————→ Futuristic
Soft    ←————————————————→ Sharp
Light   ←————————————————→ Dark
```

**Aesthetic Archetypes** (for inspiration, not prescription):

- **Brutally Minimal**: Monospace type, stark contrast, zero decoration, function as form
- **Maximalist Chaos**: Layered elements, mixed media, controlled collision, sensory richness
- **Retro-Futuristic**: Nostalgic tech aesthetics (Y2K, 80s terminal, 70s space age) with modern execution
- **Organic/Natural**: Flowing shapes, earth tones, textured surfaces, biomorphic forms
- **Luxury/Refined**: Generous whitespace, subtle details, premium typography, restrained palette
- **Playful/Toy-like**: Bright colors, rounded shapes, bouncy animations, tactile feel
- **Editorial/Magazine**: Strong typography hierarchy, grid-based, content-forward, sophisticated
- **Brutalist/Raw**: Exposed structure, system fonts, harsh colors, anti-design design
- **Art Deco/Geometric**: Bold geometry, metallic accents, symmetry, ornamental precision
- **Soft/Pastel**: Muted tones, gentle gradients, rounded edges, calm and approachable
- **Industrial/Utilitarian**: Exposed grids, monospace, warning colors, function over form

### The Memorability Test

Answer: **What's the ONE thing someone will remember about this interface?**

If the answer is "it was clean and professional," start over. Good answers:
- "The way the cards scattered like playing cards on hover"
- "That unusual green with the brutalist type"
- "How the whole page felt like a magazine spread"
- "The unexpected cursor that changed everything"

---

## 4. Design System

Create a token system BEFORE building components. This ensures consistency and enables rapid iteration.

### Spacing Scale

Use a consistent scale. Never hardcode pixel values.

```css
:root {
  --space-0: 0;
  --space-1: 4px;    /* Tight: icon padding, inline gaps */
  --space-2: 8px;    /* Compact: button padding, small gaps */
  --space-3: 12px;   /* Default: input padding, list gaps */
  --space-4: 16px;   /* Comfortable: card padding, section gaps */
  --space-5: 24px;   /* Relaxed: component separation */
  --space-6: 32px;   /* Generous: section padding */
  --space-7: 48px;   /* Spacious: major section breaks */
  --space-8: 64px;   /* Expansive: hero padding, page margins */
  --space-9: 96px;   /* Dramatic: statement spacing */
  --space-10: 128px; /* Maximum: full section breaks */
}
```

### Typography Scale

Define a clear hierarchy. Each level has a purpose.

```css
:root {
  /* Font families - CHOOSE DISTINCTIVE FONTS */
  --font-display: 'Your Display Choice', serif;
  --font-body: 'Your Body Choice', sans-serif;
  --font-mono: 'Your Mono Choice', monospace;
  
  /* Size scale */
  --text-xs: 0.75rem;    /* 12px - Captions, fine print */
  --text-sm: 0.875rem;   /* 14px - Secondary text, labels */
  --text-base: 1rem;     /* 16px - Body copy */
  --text-lg: 1.125rem;   /* 18px - Lead paragraphs */
  --text-xl: 1.25rem;    /* 20px - Small headings */
  --text-2xl: 1.5rem;    /* 24px - Section headings */
  --text-3xl: 1.875rem;  /* 30px - Page headings */
  --text-4xl: 2.25rem;   /* 36px - Hero subheads */
  --text-5xl: 3rem;      /* 48px - Hero headlines */
  --text-6xl: 3.75rem;   /* 60px - Display */
  --text-7xl: 4.5rem;    /* 72px - Statement */
  
  /* Line heights */
  --leading-none: 1;
  --leading-tight: 1.25;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;
  
  /* Font weights */
  --font-light: 300;
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;
  --font-black: 900;
}
```

### Color System

Build a purposeful palette. Dominant colors with sharp accents outperform timid, evenly-distributed palettes.

```css
:root {
  /* Surface colors (backgrounds) */
  --surface-base: /* Your base background */;
  --surface-raised: /* Elevated elements */;
  --surface-overlay: /* Modals, dropdowns */;
  
  /* Content colors (text, icons) */
  --content-primary: /* Main text */;
  --content-secondary: /* Supporting text */;
  --content-tertiary: /* Disabled, hints */;
  --content-inverse: /* Text on accent */;
  
  /* Accent colors (the personality) */
  --accent-primary: /* Main brand/action color */;
  --accent-primary-hover: /* Hover state */;
  --accent-secondary: /* Supporting accent */;
  
  /* Semantic colors */
  --semantic-success: /* Confirmations */;
  --semantic-warning: /* Cautions */;
  --semantic-error: /* Errors */;
  --semantic-info: /* Informational */;
  
  /* Border colors */
  --border-default: /* Standard borders */;
  --border-strong: /* Emphasized borders */;
  --border-focus: /* Focus rings */;
}
```

### Effects & Surfaces

```css
:root {
  /* Border radius */
  --radius-none: 0;
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-xl: 16px;
  --radius-2xl: 24px;
  --radius-full: 9999px;
  
  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px rgba(0,0,0,0.07);
  --shadow-lg: 0 10px 15px rgba(0,0,0,0.1);
  --shadow-xl: 0 20px 25px rgba(0,0,0,0.15);
  
  /* Transitions */
  --transition-fast: 150ms ease;
  --transition-base: 200ms ease;
  --transition-slow: 300ms ease;
  --transition-slower: 500ms ease;
  
  /* Z-index scale */
  --z-base: 0;
  --z-raised: 10;
  --z-dropdown: 100;
  --z-sticky: 200;
  --z-modal: 300;
  --z-toast: 400;
  --z-tooltip: 500;
}
```

**Use these tokens everywhere. Never hardcode values.**

---

## 5. Visual Execution

Now apply aesthetic craft to your UX foundation.

### Typography Craft

**Font Selection**:
- Choose fonts that are beautiful, unique, and appropriate to the tone
- **NEVER** default to: Inter, Roboto, Arial, Open Sans, system-ui
- Pair a distinctive display font with a refined body font
- Limit to 2-3 fonts maximum

**Suggested Sources**:
- Google Fonts: Libre Baskerville, Fraunces, Space Mono, Playfair Display, DM Serif, Crimson Pro, Outfit, Syne
- Consider: Variable fonts for flexibility, Display cuts for headlines

**Typographic Details**:
- Set body text at 16-18px minimum for readability
- Line length: 45-75 characters for body copy
- Use tracking (letter-spacing) intentionally: tighten headlines, loosen all-caps
- Create rhythm with consistent spacing between type elements

### Color & Theme

- Commit fully to light OR dark (or design both deliberately)
- Use your dominant color boldly, not timidly
- Accent colors should pop—they're for emphasis, not decoration
- Test contrast ratios (4.5:1 for text, 3:1 for large text/UI)

### Motion & Animation

**Priority Order** (focus here first):
1. **Page load orchestration**: Staggered reveals create delight
2. **State transitions**: Smooth changes between component states
3. **Scroll interactions**: Parallax, reveal-on-scroll, progress indicators
4. **Hover/focus states**: Confirm interactivity
5. **Micro-interactions**: Polish (button bounces, icon morphs)

**Performance Rules**:
- Animate only `transform` and `opacity` for 60fps
- Use CSS transitions for simple state changes
- Use CSS animations or Motion library for complex sequences
- Respect `prefers-reduced-motion`:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Spatial Composition

Break free from predictable layouts:
- **Asymmetry**: Balance without mirror symmetry
- **Overlap**: Elements breaking container boundaries
- **Diagonal flow**: Breaking the horizontal/vertical grid
- **Grid-breaking moments**: Strategic elements that defy the system
- **Whitespace**: Generous negative space OR controlled density—both work

### Backgrounds & Atmosphere

Don't default to solid colors. Create depth:
- Gradient meshes and organic gradients
- Noise and grain textures
- Geometric patterns (subtle or bold)
- Layered transparencies
- Dramatic shadows and lighting effects
- Decorative borders and dividers
- Custom cursors
- Background blur (glassmorphism—use sparingly)

---

## 6. Responsive Design

Design for all viewports intentionally.

### Strategy

**Mobile-First**: Start with smallest viewport, enhance upward. This forces prioritization.

**Breakpoint Thinking**:
```css
/* Base: Mobile (default styles) */

/* Tablet: When layout needs change */
@media (min-width: 768px) { }

/* Desktop: Full experience */
@media (min-width: 1024px) { }

/* Large: When you have luxury of space */
@media (min-width: 1280px) { }
```

Define breakpoints based on YOUR content, not arbitrary device sizes.

### Responsive Considerations

| Element | Mobile Approach | Desktop Approach |
|---------|-----------------|------------------|
| Navigation | Hamburger, bottom nav, or simplified | Full horizontal, mega menus |
| Typography | Smaller scale, tighter spacing | Full scale, generous spacing |
| Layout | Single column, stacked | Multi-column, grid |
| Touch targets | 44x44px minimum | Can be smaller |
| Images | Smaller files, art direction | Full resolution |
| Interactions | Tap, swipe | Hover states, keyboard |

### What Changes Between Viewports?

Not just scaling—rethink:
- What gets hidden on mobile? (Only non-essential items)
- What changes position? (Key actions stay accessible)
- What changes interaction pattern? (Hover → tap)
- What gets simplified? (Dense tables → cards)

---

## 7. Accessibility

Accessibility is design quality, not a checklist afterthought.

### Non-Negotiables

**Color Contrast**:
- 4.5:1 ratio for normal text (< 18px or < 14px bold)
- 3:1 ratio for large text and UI components
- Test with WebAIM contrast checker or similar

**Focus Indicators**:
```css
/* NEVER do this */
*:focus { outline: none; }

/* DO this - clear, visible focus */
:focus-visible {
  outline: 2px solid var(--border-focus);
  outline-offset: 2px;
}
```

**Semantic HTML**:
```html
<!-- Use proper elements -->
<button> not <div onclick>
<a href> not <span onclick>
<nav>, <main>, <article>, <aside>, <header>, <footer>
<h1> → <h6> in order (no skipping levels)
<ul>/<ol> for lists
<table> for tabular data (with proper headers)
```

**Form Accessibility**:
```html
<!-- Every input needs a label -->
<label for="email">Email address</label>
<input id="email" type="email" required>

<!-- Or wrapped -->
<label>
  Email address
  <input type="email" required>
</label>

<!-- Error messages linked -->
<input aria-describedby="email-error" aria-invalid="true">
<span id="email-error">Please enter a valid email</span>
```

**Keyboard Navigation**:
- All interactive elements reachable via Tab
- Logical tab order (usually DOM order)
- Escape closes modals/dropdowns
- Arrow keys for menus, tabs, sliders
- Enter/Space activates buttons and links

### ARIA When Needed

Use ARIA to fill gaps, not replace semantics:

```html
<!-- Live regions for dynamic updates -->
<div aria-live="polite">Items in cart: 3</div>

<!-- Labels for icon-only buttons -->
<button aria-label="Close menu">
  <svg>...</svg>
</button>

<!-- Descriptions for complex elements -->
<input aria-describedby="password-requirements">
<div id="password-requirements">Must be 8+ characters...</div>
```

### Testing

- Navigate entire UI with keyboard only
- Use screen reader (VoiceOver, NVDA) to verify announcements
- Check with browser accessibility inspector
- Test at 200% zoom

---

## 8. Performance

Fast is a feature. Performance is UX.

### Loading Strategy

**Critical Path**:
- Above-fold content loads first
- Defer non-essential JS
- Lazy-load images below fold
- Inline critical CSS

**Font Loading**:
```css
@font-face {
  font-family: 'Display Font';
  src: url('font.woff2') format('woff2');
  font-display: swap; /* Show fallback immediately */
}
```

Limit to 2-3 font files. Variable fonts reduce requests.

**Image Optimization**:
```html
<!-- Responsive images -->
<img 
  src="image-800.webp"
  srcset="image-400.webp 400w, image-800.webp 800w, image-1200.webp 1200w"
  sizes="(max-width: 600px) 100vw, 800px"
  alt="Descriptive alt text"
  loading="lazy"
>
```

### Animation Performance

Only animate `transform` and `opacity`—these are GPU-accelerated:

```css
/* GOOD - GPU accelerated */
.card:hover {
  transform: translateY(-4px) scale(1.02);
  opacity: 0.95;
}

/* AVOID - triggers layout/paint */
.card:hover {
  top: -4px;
  width: 102%;
  background: #fff;
}
```

### CSS Efficiency

- Avoid deeply nested selectors
- Minimize repaints (changes to color, shadow)
- Minimize reflows (changes to size, position)
- Use `contain` for isolated components
- Use `will-change` sparingly and only when needed

---

## 9. Anti-Patterns

**NEVER create these generic "AI slop" outputs:**

### Typography Sins
- ❌ Inter, Roboto, Arial, system-ui as primary fonts
- ❌ Generic font pairings (Inter + Roboto)
- ❌ Default browser sizes without intentional scale
- ❌ Inconsistent heading hierarchy

### Color Sins
- ❌ Purple-to-blue gradients on white (the #1 AI tell)
- ❌ Timid, evenly-distributed palettes
- ❌ Random color choices without cohesion
- ❌ Insufficient contrast

### Layout Sins
- ❌ Identical card grids everywhere
- ❌ Centered everything
- ❌ Predictable header/hero/3-cards/CTA/footer formula
- ❌ No visual hierarchy—everything same weight

### Interaction Sins
- ❌ No hover states
- ❌ No focus indicators
- ❌ No loading states
- ❌ Missing error handling
- ❌ Buttons that don't look clickable

### Craft Sins
- ❌ Placeholder content left in ("Lorem ipsum")
- ❌ Misaligned elements
- ❌ Inconsistent spacing
- ❌ Converging on the same "safe" choices across outputs

**Every design should be unique. Vary themes, fonts, aesthetics. Never converge on comfortable defaults.**

---

## 10. Pre-Implementation Checklist

Before writing code, confirm:

### UX Foundation
- [ ] Primary user action is clear (ONE thing)
- [ ] Information hierarchy is defined (1st, 2nd, 3rd)
- [ ] User flow is mapped (entry → action → success)
- [ ] All component states are planned (default, hover, focus, active, disabled, loading, error, success, empty)

### Design Direction
- [ ] Aesthetic direction is committed (specific, not "clean and modern")
- [ ] Reference images identified (3-5 inspirations)
- [ ] Memorable element defined (the ONE thing people will remember)
- [ ] Color palette chosen with clear dominant/accent hierarchy

### Technical Foundation
- [ ] Design tokens defined (spacing, typography, colors)
- [ ] Responsive strategy decided (mobile-first breakpoints)
- [ ] Accessibility requirements identified (WCAG AA minimum)
- [ ] Performance constraints noted (font budget, image strategy)

---

## Implementation Notes

### HTML Artifacts
- Single file: HTML + CSS + JS inline
- Use CSS animations over JS when possible
- External scripts from cdnjs.cloudflare.com only

### React Artifacts
- Single file with Tailwind utilities (core classes only)
- Motion library available for complex animations
- Available: lucide-react, recharts, d3, three.js, shadcn/ui
- No localStorage/sessionStorage (use React state)

### Quality Bar

Claude is capable of extraordinary creative work. The goal is production-grade interfaces that are:
- **Functional**: Everything works, all states handled
- **Accessible**: Keyboard navigable, screen reader compatible
- **Performant**: Fast load, smooth interactions
- **Distinctive**: Memorable, unique, clearly designed for this specific context
- **Polished**: Attention to detail in every element

Don't hold back. Show what's possible when committing fully to craft.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pbdigital) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
