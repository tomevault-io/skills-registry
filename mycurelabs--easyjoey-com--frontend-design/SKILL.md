---
name: frontend-design
description: Creates distinctive, production-grade frontend interfaces with high design quality for TOPSI Inc. products. Auto-activates for UI design, landing pages, dashboards, patient portals, and healthcare interfaces. Focuses on avoiding generic "AI slop" aesthetics through intentional creative direction, accessibility, and MYCURE healthcare adaptations.
metadata:
  author: mycurelabs
---

# Frontend Design Skill

Generate distinctive, high-quality frontend interfaces that avoid generic "AI slop" aesthetics and align with TOPSI Inc. brand standards and healthcare usability requirements.

## When This Skill Activates

- Creating or updating UI components, pages, or interfaces
- Designing landing pages, dashboards, or web applications
- Building patient-facing healthcare interfaces (MYCURE products)
- Implementing design systems or style guides
- Working on frontend code with visual/aesthetic components

## Core Philosophy: Counteract Distributional Convergence

AI models tend toward "safe" design choices that work universally but lack character. **Your task is to break this pattern** by:

1. **Understanding context**: What is this product? Who uses it? What should they feel?
2. **Committing to aesthetic tone**: Choose a bold direction (not "neutral professional")
3. **Technical intentionality**: Every design choice should have a purpose
4. **Memorable differentiation**: What makes THIS interface unique and recognizable?

---

## Design Vectors

### 1. Typography: Choose Distinctive, Characterful Fonts

#### ❌ NEVER Use These Generic Fonts
- Inter, Roboto, Arial, Helvetica
- Open Sans, Lato, Montserrat
- "Safe" system font stacks without character

#### ✅ PREFER Distinctive, Purpose-Driven Fonts

**For Technical/Medical Products (MYCURE):**
- **JetBrains Mono** - Code, technical data, medical records
- **IBM Plex Sans/Serif** - Clean, technical, trustworthy
- **Source Sans Pro** - Healthcare documentation
- **Atkinson Hyperlegible** - Maximum accessibility for patient-facing

**For Editorial/Marketing:**
- **Playfair Display** - Elegant serif headings
- **Crimson Pro** - Editorial content, long-form reading
- **Bricolage Grotesque** - Distinctive sans with character

**For Brand Expression:**
- **Space Grotesk** - Modern, geometric
- **Syne** - Bold headings with personality
- **DM Sans** - If you must use a geometric sans, this over Inter

####  Implementation Techniques

**Extreme Contrasts:**
```css
/* Display vs. body - 3x+ scale ratio */
h1 { font-size: 72px; font-weight: 700; } /* Display serif */
p  { font-size: 18px; font-weight: 400; } /* Monospace body */
```

**Variable Weights:**
```css
/* Use full weight range (100-900), not just 400-600 */
--heading: 800;
--subheading: 600;
--body: 400;
--caption: 300;
```

**Loading Fonts:**
```html
<!-- Google Fonts - decisive single-family application -->
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;600;800&display=swap">
```

---

### 2. Color & Theming: Narrative-Driven CSS Variables

#### ❌ AVOID Bland, Neutral Palettes
- All grays and one accent color
- Purple gradients on white backgrounds
- "Corporate blue" without context

#### ✅ USE Context-Driven, Memorable Palettes

**Approach:**
1. **Anchor to narrative/purpose**: What feeling or context should colors evoke?
2. **Dominant colors + sharp accents**: Not timid 50/50 distributions
3. **CSS variables for consistency**

**Example: Healthcare/Clinical (MYCURE)**
```css
:root {
  /* Clinical Trust - Blues and whites with medical green accents */
  --clinical-blue: #0066CC;
  --trust-navy: #003366;
  --medical-green: #00A86B;
  --alert-red: #DC3545;
  --warn-amber: #FFC107;

  /* Surfaces */
  --surface-primary: #FFFFFF;
  --surface-secondary: #F8F9FA;
  --surface-tertiary: #E9ECEF;

  /* Text */
  --text-primary: #212529;
  --text-secondary: #6C757D;
  --text-on-color: #FFFFFF;
}
```

**Example: Government/LGU (MYCURE Gov)**
```css
:root {
  /* Philippine flag inspired - Authority with accessibility */
  --gov-blue: #0038A8;
  --gov-red: #CE1126;
  --sunburst-gold: #FCD116;
  --service-green: #00A86B;

  /* High contrast for accessibility */
  --surface: #FFFFFF;
  --text: #1A1A1A;
}
```

**Example: RPG/Gaming Aesthetic** (if applicable to other products)
```css
:root {
  --mud: #8B4513;
  --gold: #DAA520;
  --parchment: #F4E8D0;
  --shadow: #2C2416;
}
```

#### Advanced Theming

**Dark Mode Toggle:**
```css
[data-theme="dark"] {
  --surface-primary: #1A1A1A;
  --text-primary: #F8F9FA;
  /* Invert but maintain accent colors */
}
```

**Gradients & Depth:**
```css
.hero-background {
  background:
    radial-gradient(circle at top right, var(--clinical-blue), transparent),
    radial-gradient(circle at bottom left, var(--medical-green), transparent),
    var(--surface-primary);
  background-blend-mode: multiply;
}
```

---

### 3. Motion & Animation: CSS-First, Purposeful Reveals

#### ❌ AVOID
- No animations (flat, lifeless)
- Micro-animations on every element (distracting)
- JavaScript-heavy animations (performance issues)

#### ✅ USE Orchestrated, High-Impact Moments

**Philosophy:**
- **CSS-first for HTML**: Native keyframes, `cubic-bezier` easing
- **React Motion** for React components only
- **One well-orchestrated page load** with staggered reveals
- **Purposeful animations** that enhance, not distract

**Page Load Stagger:**
```css
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.hero-title {
  animation: fadeInUp 0.6s cubic-bezier(0.4, 0, 0.2, 1) 0.1s both;
}

.hero-subtitle {
  animation: fadeInUp 0.6s cubic-bezier(0.4, 0, 0.2, 1) 0.3s both;
}

.hero-cta {
  animation: fadeInUp 0.6s cubic-bezier(0.4, 0, 0.2, 1) 0.5s both;
}
```

**Healthcare-Specific: Calm, Predictable Animations**
```css
/* Patients need calm, not flashy */
.patient-card {
  transition: all 0.3s ease;
}

.patient-card:hover {
  transform: translateY(-2px); /* Subtle, not jarring */
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}
```

**Critical Actions: Clear Feedback**
```css
.submit-button {
  transition: background-color 0.2s ease, transform 0.1s ease;
}

.submit-button:active {
  transform: scale(0.98); /* Clear press feedback */
}

.submit-button.loading {
  animation: pulse 1.5s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.7; }
}
```

---

### 4. Spatial Composition: Break the Grid

####  AVOID Predictable Layouts
- Everything centered and symmetrical
- Perfect alignment grids
- Equal spacing everywhere
- No visual hierarchy

#### ✅ USE Unexpected, Intentional Asymmetry

**Techniques:**
- **Asymmetry**: Primary content left, secondary right (or vice versa)
- **Overlap**: Elements layered with z-index depth
- **Diagonal flow**: Guides eye movement through page
- **Grid-breaking elements**: Some items break out of grid constraints
- **Varied spacing**: Intentional whitespace creates rhythm

**Example: Dashboard Card Layout**
```css
.dashboard-grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
  grid-template-rows: auto auto;
  gap: 24px;
}

.primary-metric {
  grid-column: 1 / 2;
  grid-row: 1 / 3; /* Spans two rows - asymmetry */
}

.secondary-metric {
  grid-column: 2 / 3;
  grid-row: 1 / 2;
}

.trend-chart {
  grid-column: 2 / 3;
  grid-row: 2 / 3;
}

.feature-highlight {
  grid-column: 1 / 3; /* Breaks out - full width */
  margin-left: -10%; /* Breaks container */
  padding: 48px;
  background: var(--accent);
  transform: rotate(-0.5deg); /* Subtle diagonal */
}
```

---

### 5. Visual Details: Layered Depth

#### Add Subtle Richness

**Gradients:**
```css
.card {
  background: linear-gradient(135deg, #FFFFFF 0%, #F8F9FA 100%);
}
```

**Noise Textures:**
```css
.hero {
  background-image:
    url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="300" height="300"><filter id="noise"><feTurbulence type="fractalNoise" baseFrequency="0.9" numOctaves="4" /></filter><rect width="300" height="300" filter="url(%23noise)" opacity="0.05"/></svg>'),
    linear-gradient(135deg, var(--primary), var(--secondary));
}
```

**Patterns:**
```css
.background-pattern {
  background-image:
    repeating-linear-gradient(90deg, transparent, transparent 50px, rgba(0,0,0,0.02) 50px, rgba(0,0,0,0.02) 51px);
  background-size: 100px 100px;
}
```

**Layered Transparencies:**
```css
.glass-card {
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}
```

**Custom Cursors (where appropriate):**
```css
.interactive-area {
  cursor: url('custom-cursor.svg'), pointer;
}
```

---

## Healthcare-Specific Adaptations (MYCURE)

### Accessibility is Mandatory

**WCAG 2.2 Level AA Requirements:**
- Color contrast: 4.5:1 for normal text, 3:1 for large text
- All interactive elements keyboard accessible
- Screen reader compatible (ARIA labels)
- Clear focus indicators
- Text resizable to 200% without breaking layout

**Implementation:**
```css
/* High contrast focus indicators */
*:focus-visible {
  outline: 3px solid var(--medical-green);
  outline-offset: 2px;
}

/* Ensure sufficient contrast */
.button-primary {
  background: var(--clinical-blue); /* #0066CC */
  color: white; /* Contrast ratio: 7.5:1 - Exceeds AA */
}
```

### Patient Safety Language

**Visual Hierarchy for Critical Information:**
```css
.alert-critical {
  background: var(--alert-red);
  color: white;
  font-weight: 700;
  padding: 16px;
  border-left: 4px solid darkred;
  font-size: 18px; /* Larger for visibility */
}

.alert-warning {
  background: #FFF3CD;
  color: #856404;
  border-left: 4px solid var(--warn-amber);
}

.alert-info {
  background: #D1ECF1;
  color: #0C5460;
  border-left: 4px solid var(--clinical-blue);
}
```

### Medical Data Presentation

**Clarity Over Style for Data:**
```css
.medical-record {
  font-family: 'JetBrains Mono', monospace; /* Monospace for data */
  font-size: 16px;
  line-height: 1.6; /* Readable line height */
  letter-spacing: 0.02em; /* Slight tracking for clarity */
}

.data-label {
  font-weight: 600;
  color: var(--text-secondary);
  text-transform: uppercase;
  font-size: 12px;
  letter-spacing: 0.08em;
}

.data-value {
  font-weight: 400;
  color: var(--text-primary);
  font-size: 18px;
}
```

### Philippine LGU/Government Contexts

**High-Contrast for Variable Display Conditions:**
- Assume low-quality displays
- Strong color contrasts
- Larger minimum font sizes (16px base, not 14px)
- Avoid subtle grays (use #6C757D minimum, not #CCCCCC)

**Low-Bandwidth Considerations:**
- CSS animations only (no video backgrounds)
- Optimized images with WebP fallbacks
- System fonts as fallbacks
- Minimal JavaScript for critical paths

---

## Anti-Patterns: Never Do These

1. **Generic Font Stacks**: Never default to Inter/Roboto/Arial without intentional choice
2. **Purple Gradients on White**: Overused AI aesthetic cliché
3. **Center-Everything Layouts**: Boring, predictable symmetry
4. **Flat Solids**: No depth, visual interest, or texture
5. **Micro-Animations Everywhere**: Distracting and performant issues
6. **Low-Contrast "Minimal" Design**: Inaccessible and hard to read
7. **Cookie-Cutter Templates**: Every design should have context-specific character
8. **Ignoring Accessibility**: Never sacrifice usability for aesthetics

---

## Implementation Workflow

### 1. Understand Context
- What is this product/feature?
- Who uses it? (Demographics, technical literacy, use cases)
- What should they feel? (Trust, efficiency, calm, excitement)
- What constraints exist? (Technical, regulatory, performance)

### 2. Choose Aesthetic Direction
**For MYCURE Clinical:**
- **Feeling**: Trust, clarity, calm professionalism
- **Typography**: JetBrains Mono + IBM Plex Sans
- **Colors**: Clinical blues, medical greens, high-contrast
- **Motion**: Subtle, predictable, calm
- **Layout**: Clear hierarchy, data-focused

**For MYCURE Gov:**
- **Feeling**: Authority, service, accessibility
- **Typography**: Atkinson Hyperlegible + Source Sans Pro
- **Colors**: Philippine flag-inspired, high contrast
- **Motion**: Minimal, performance-optimized
- **Layout**: Simple, scannable, mobile-first

### 3. Technical Implementation
- Load fonts via Google Fonts or local @font-face
- Define CSS variables for theme in `:root`
- Implement responsive breakpoints (mobile-first)
- Add WCAG-compliant contrast ratios
- Test on low-bandwidth connections
- Validate accessibility with screen readers

### 4. Iteration & Refinement
- Test with actual users (patients, healthcare workers, LGU staff)
- Validate accessibility with automated tools + manual testing
- Optimize performance (lighthouse scores)
- Refine based on feedback

---

## Resources

**Typography:**
- [reference/typography-guide.md](reference/typography-guide.md)
- Google Fonts: https://fonts.google.com/

**Color Systems:**
- [reference/color-systems.md](reference/color-systems.md)
- Coolors Palette Generator: https://coolors.co/
- WCAG Contrast Checker: https://webaim.org/resources/contrastchecker/

**Animation:**
- [reference/animation-patterns.md](reference/animation-patterns.md)
- Cubic Bezier Generator: https://cubic-bezier.com/
- CSS Easing Functions: https://easings.net/

**Healthcare Examples:**
- [examples/healthcare-dashboard.md](examples/healthcare-dashboard.md)
- [examples/patient-portal-ui.md](examples/patient-portal-ui.md)

**Accessibility:**
- WCAG 2.2 Guidelines: https://www.w3.org/WAI/WCAG22/quickref/
- Atkinson Hyperlegible Font: https://brailleinstitute.org/freefont

---

## Summary Checklist

When generating frontend UI, ensure:

- [ ] **Typography**: Distinctive fonts chosen for purpose (not Inter/Roboto)
- [ ] **Color**: Context-driven palette with CSS variables
- [ ] **Motion**: CSS-first animations, purposeful reveals
- [ ] **Layout**: Intentional asymmetry, visual hierarchy
- [ ] **Depth**: Gradients, textures, layered elements
- [ ] **Accessibility**: WCAG 2.2 Level AA compliance
- [ ] **Healthcare**: Patient safety language, data clarity
- [ ] **Performance**: Optimized for low-bandwidth (if LGU/Gov)
- [ ] **Character**: Unique to this product, not generic template

**Remember**: Every design choice should have a reason beyond "it's safe." Break the mold. Create something memorable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mycurelabs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
