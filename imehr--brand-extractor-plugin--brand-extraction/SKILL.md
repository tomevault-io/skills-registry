---
name: brand-extraction
description: Extract complete design systems and brand identities from URLs using Playwright, CSS analysis, and AI interpretation. Covers colour, typography, spacing, components, brand voice, and accessibility. Use when extracting design tokens from websites, reverse-engineering design systems, creating brand documentation from live sites, or building component replications from extracted tokens. Use when this capability is needed.
metadata:
  author: imehr
---

# Brand Extraction Methodology

This skill teaches Claude how to extract, analyse, and document a complete design system from any given URL. It combines automated CSS/DOM extraction with expert-level AI interpretation to produce the same quality of output a senior design system engineer would deliver.

## Expert Process Overview

The extraction follows the same five phases a human expert would use, enhanced by automation:

1. **Reconnaissance & Inventory** — Browse, screenshot, identify page types, locate brand resources
2. **Token Extraction** — CSS custom properties, computed styles, frequency analysis, confidence scoring
3. **Component Cataloguing** — Identify atoms, molecules, organisms per atomic design methodology
4. **Brand Voice Analysis** — Tone dimensions, vocabulary, CTA patterns, language variant
5. **Validation & Replication** — Build components from tokens, compare visually, iterate until correct

**Critical insight:** Experts spend 40–60% of total time on validation and iterative refinement, not initial extraction. The extraction is the easy part. Getting it right is the hard part.

## Technology Stack

Install before first use:

```bash
pip install playwright beautifulsoup4 lxml Pillow pixelmatch tinycss2 colormath Jinja2 requests
playwright install chromium
```

## Extraction Pipeline

### Step 1: Discovery & Reconnaissance

```bash
python scripts/extract_tokens.py --stage recon --url <target_url>
```

Actions:
- Navigate with Playwright (Chromium, stealth mode for bot detection bypass)
- Wait for SPA hydration: 8s initial + 4s stabilisation
- Extract `<head>` metadata: title, OG tags, favicon links, theme-colour, stylesheet URLs, font preconnects
- Search for brand guidelines: `"{brand}" design system`, `"{brand}" style guide`, `site:{domain} brand`
- Crawl internal links to identify page types (home, content, product, form, error)
- Screenshot at 3 breakpoints: Desktop 1440×900, Tablet 768×1024, Mobile 390×844

**→ Validated by Gate 1** (see [validation-rubric.md](validation-rubric.md))

### Step 2: CSS & Token Extraction

```bash
python scripts/extract_tokens.py --stage tokens --url <target_url>
```

Actions:
- Extract all `--*` CSS custom properties from `:root`, `html`, `body`
- Computed styles analysis on every visible element: colour, typography, spacing, radius, shadow, border, transition
- Frequency analysis: count occurrences to find the real system scales
- Confidence scoring:
  - **HIGH**: CSS custom property OR 5+ occurrences across different element types
  - **MEDIUM**: 2–4 consistent occurrences
  - **LOW**: One-off values (likely not part of the system)
- Colour deep dive: hex + oklch normalisation, hue grouping, semantic role detection (brand/neutral/feedback/surface), dark mode (`prefers-color-scheme` or `.dark` class)
- Typography deep dive: font families (primary/secondary/mono), full type scale, heading hierarchy (h1–h6), line heights, letter spacing, font weights, font-feature-settings
- Spacing deep dive: GCD analysis for base unit (4px or 8px typical), scale generation, container max-widths, grid system detection
- Borders, shadows, breakpoints, transitions/animations

**→ Validated by Gate 2**

### Step 3: Asset Extraction

```bash
python scripts/extract_tokens.py --stage assets --url <target_url>
```

Actions:
- Logo detection in `<header>`, `<nav>`, `[role="banner"]` — extract SVG source or highest-res raster
- Favicon: all `<link rel="icon">` variants, apple touch icons, manifest.json icons
- Icon system: Font Awesome, Material Icons, custom SVG sprite, or none
- Dark mode logo variation check

**→ Validated by Gate 3**

### Step 4: Content & Voice Analysis (Parallel)

Actions:
- Scrape all visible text, categorise: headings, body, CTAs, nav labels, form labels, footer
- LLM analysis: tone dimensions (formal↔casual, technical↔accessible, authoritative↔friendly, urgent↔calm)
- Voice characteristics: 3–5 defining traits with evidence (quotes under 14 words)
- Language variant detection: Australian/American/British English (spelling patterns)
- CTA pattern analysis: verb usage, urgency, personalisation
- Microcopy: button labels, validation messages, empty states, tooltips

### Step 5: AI Synthesis & Token Generation

Actions:
- Curate colour palette: noise removal, semantic naming (`{category}.{role}`), ramp generation, WCAG contrast
- Formalise type scale: rationalise to nearest standard ratio (major third 1.25, perfect fourth 1.333, etc.)
- Normalise spacing: canonical scale from base unit
- Component-token mapping
- Export W3C DTCG `.tokens.json` (2025.10 spec) — see [design-tokens skill](../design-tokens/SKILL.md)
- Export CSS custom properties file

**→ Validated by Gate 4**

### Step 6: Component Replication

```bash
python scripts/screenshot_components.py
python scripts/compare_visual.py
```

Actions:
- Generate standalone HTML/CSS for: nav bar, hero section, button set (all variants), card, footer, form elements
- **Every CSS value must reference a design token — zero hard-coded values**
- Screenshot replications at matching viewport sizes
- Three-layer validation: pixel comparison (pixelmatch), structural comparison (LLM visual), token traceability

**→ Validated by Gate 5** (see [visual-validation skill](../visual-validation/SKILL.md))

### Step 7: SVG Logo Extraction & Design System HTML

#### 7a: SVG Logo Extraction

Actions:
- Parse `assets/logo-candidates.json` from Step 3
- Select the best SVG candidate (prefer inline SVG, largest viewBox)
- Save as `assets/{brand}-logo.svg` (standalone, clean SVG file)
- If no SVG available, save best raster and note the gap

#### 7b: HTML Design System Generation

Actions:
- Generate a fully self-contained `design-system.html` at the extraction root
- Use extracted tokens as CSS custom properties throughout
- Include all sections: colours (swatches + WCAG), typography (specimens + scale), spacing (visual bars), radius, buttons (live demos), navigation (replica), cards, assets (SVG at multiple sizes), voice (tone spectrum + CTAs), screenshots (responsive grid), validation (gate results), token reference (tables + confidence badges)
- Add sticky TOC with IntersectionObserver-based active state
- Brand the HTML using the extracted colours (header, accents)
- No external dependencies — all CSS and JS inline
- Reference template: `docs/allianz/extraction/design-system.html` (if available in project)

**This step is mandatory.** The HTML design system is a primary deliverable alongside the markdown brand document and token files.

### Step 8: Final Document Assembly

Produce the comprehensive brand document (markdown) with inline validation evidence at every section. Reference the `design-system.html` and extracted SVG logo. See document structure specification in the brief.

## Confidence Scoring Framework

| Level | Criteria | Action |
|---|---|---|
| HIGH | CSS custom property OR 5+ occurrences across element types | Include in design system |
| MEDIUM | 2–4 consistent occurrences | Include with note |
| LOW | One-off value | Exclude unless manually confirmed |

## Noise Filtering

Exclude from the design system:
- Colours from known third-party widgets (Intercom, Drift, cookie banners, ad networks)
- Default browser link blue (`#0000EE`) and visited purple
- Transparent/inherit/initial values
- Inline styles on third-party iframes
- Values only appearing in `.ad-`, `.tracking-`, `.cookie-` prefixed classes

## Supporting References

- [validation-rubric.md](validation-rubric.md) — Complete 5-gate validation criteria
- [component-taxonomy.md](component-taxonomy.md) — Atomic design component classification

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imehr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
