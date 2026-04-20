---
name: design-excellence
description: Automatically apply professional design thinking to any visual output. Activates when creating presentations (.pptx), dashboards, reports, HTML pages/artifacts, PDFs, spreadsheets (.xlsx), data visualizations, landing pages, UI mockups, or any request for visual/designed content. Transforms generic output into polished, hand-crafted results by applying systematic design refinement, interrogating every visual choice, and referencing professional design exemplars (Stripe, Linear, Apple, Swiss design). The user sees only polished results unless they request to see the design process. Use when this capability is needed.
metadata:
  author: vuonghv
---

# Design Excellence

Transform visual outputs from functional drafts to polished, professional results through systematic design refinement.

## Workflow

### 1. Silent Activation

When user requests visual output (presentation, dashboard, HTML, PDF, spreadsheet, etc.):
- Activate this skill automatically
- Do NOT announce activation to user
- User sees only the final polished result
- Only reveal process if user explicitly asks "show me your design thinking"

### 2. Foundation Pass

Before any visual work:
1. Identify the ONE primary message/purpose
2. Ruthlessly prioritize content hierarchy
3. Define primary and secondary actions (if applicable)
4. Cut anything non-essential

### 3. Design System Selection

Choose approach based on context:

| Context | Reference Style | Key Moves |
|---------|-----------------|-----------|
| SaaS/Dashboard | Linear | Restraint, density, monochrome + accent |
| Marketing/Landing | Apple/Stripe | Space, imagery, gradient sophistication |
| Data/Reports | Swiss | Grid, objectivity, clear hierarchy |
| Developer/Technical | Vercel | Black/white, monospace, sharp edges |
| Collaborative/Creative | Figma | Rounded, colorful, approachable |

### 4. Core Decisions

Make these deliberately (never accept defaults):

**Typography**
- Select specific typeface with intention (not system default)
- Define scale: 12/14/16/20/24/32/48 minimum
- Adjust letter-spacing: tighten headlines, widen small text

**Color**
- Limit to 3-5 colors maximum
- Assign functional roles (action, neutral, accent)
- Never pure black (#000); use near-black (#1a1a1a)
- Verify 4.5:1 contrast ratios

**Spacing**
- Choose base unit (8px standard)
- Apply multiples consistently: 8/16/24/32/48/64
- When uncertain, add more white space

**Layout**
- Establish grid (12-column or 6-column)
- Align everything to grid
- Create clear focal point

### 5. Elevation Sequence

Apply in order:

1. **Structure**: Grid, alignment, content blocks
2. **Typography**: Scale, weights, spacing refinements
3. **Color**: Limited palette, functional assignment
4. **Space**: Generous margins, consistent rhythm
5. **Polish**: Shadows, borders, corners, micro-details

### 6. Quality Gate

Before delivering, verify:
- [ ] Single clear focal point exists
- [ ] Nothing feels arbitrary or default
- [ ] Would pass review by Stripe/Linear design team
- [ ] Every element earns its space
- [ ] Feels hand-crafted, not template-based

## Reference Files

Consult as needed during refinement:

| File | When to Use |
|------|-------------|
| [interrogation-checklist.md](references/interrogation-checklist.md) | Final quality check before delivery |
| [technique-catalog.md](references/technique-catalog.md) | Need specific technique for a problem |
| [reference-library.md](references/reference-library.md) | Choosing style direction, studying exemplars |
| [elevation-protocol.md](references/elevation-protocol.md) | Detailed step-by-step refinement process |
| [design-philosophy.md](references/design-philosophy.md) | Guidance on restraint, confidence, taste |

## Quick Reference: Common Moves

### "Looks Generic" → Apply
- Tighten headline letter-spacing (-0.02em)
- Replace pure black with warm/cool near-black
- Add generous white space (double what feels right)
- Pick ONE distinctive choice (type, color, or layout)

### "Too Busy" → Apply
- Remove decorative elements
- Reduce to 3 colors maximum
- Increase spacing between sections
- Cut text by 30%

### "Lacks Polish" → Apply
- Align everything to 8px grid
- Add subtle shadows (0 1px 3px rgba(0,0,0,0.08))
- Unify corner radius (pick one: 0/4px/8px)
- Match icon weight to font weight

### "Feels Flat" → Apply
- Add elevation through subtle shadow layers
- Create color contrast between sections
- Vary section backgrounds (alternating light)
- Introduce one bold accent element

## Output Standards

All visual outputs must:
1. Have clear hierarchy visible in 3-second scan
2. Use consistent spacing throughout (no arbitrary gaps)
3. Apply color intentionally (every color has a reason)
4. Feel distinctive (not recognizable as template)
5. Prioritize clarity over decoration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuonghv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
