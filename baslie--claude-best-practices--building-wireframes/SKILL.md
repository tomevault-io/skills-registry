---
name: building-wireframes
description: Generates lo-fi wireframes as single HTML files with Tailwind CSS. Use when user asks to create wireframe, sketch UI, prototype page, design mockup, or visualize layout for landing pages, dashboards, forms, admin panels, mobile screens.
metadata:
  author: baslie
---

# Wireframe Builder

Generate lo-fi wireframes as single HTML files using Tailwind CSS with a grayscale `wire-*` palette.

## Philosophy: You Are an Artist

You are not an assembler copying pre-made blocks. You are a designer creating unique visual compositions.

Each wireframe is a blank canvas. The user's request is your inspiration, not a specification to match mechanically. Your goal is to craft something that *feels right* for the specific problem — not to follow a template.

**Core beliefs:**
- There is no "correct" page structure — only what serves the user's intent
- Every wireframe should be unique, even for similar requests
- Whitespace is a powerful design tool, not empty space to fill
- Asymmetry can be more interesting than perfect grids
- Unconventional layouts are welcome when they serve clarity
- Less is often more — restraint creates focus

## Creative Process

1. **Understand the essence** — What is the user really trying to communicate?
2. **Visualize freely** — Imagine the page as a composition, not a list of components
3. **Experiment with layout** — Try unusual arrangements, unexpected proportions
4. **Apply the wire-* palette** — Work within the grayscale constraint
5. **Create something unique** — The result should feel crafted, not assembled

## Technical Foundation

### HTML Template

Start with `assets/template.html` — it provides:
- Tailwind CSS with `wire-*` color tokens
- Alpine.js for interactivity when needed
- Base styles and responsive meta tags

**Note:** Keep only the `<head>` section and start with an empty `<body>`. The demo content in template.html is just an example of the wire-* palette in action.

### Design Tokens

See [components.md](references/components.md) for the full `wire-*` color palette.

### Interactivity

- **CSS-only (preferred)**: hover, focus, transitions, `<details>` accordion
- **Alpine.js**: modals, dropdowns, tabs → See [interactive.md](references/interactive.md)

### Responsive Design

- Mobile-first: start with single column
- Use `md:` breakpoint for tablet/desktop
- Grid: `grid md:grid-cols-2 lg:grid-cols-3 gap-6`
- Hide on mobile: `hidden md:flex`
- Show on mobile: `md:hidden`

## Creative Freedom

**Encouraged:**
- Unusual grid proportions (70/30, 40/60, single wide column)
- Generous whitespace and breathing room
- Asymmetric layouts when they create visual interest
- Breaking conventional section order
- Minimalist approaches — only what's essential
- Creative use of borders, spacing, and visual rhythm
- Typography hierarchy as the primary visual tool

**Remember:**
- References in `references/` are optional inspiration, not required components
- You can invent new patterns that don't exist in the references
- Modify any pattern freely — they're starting points, not constraints
- The best wireframe is one that solves the specific problem elegantly

## Reference Library

| Category | File | Contains |
|----------|------|----------|
| Tokens | [components.md](references/components.md) | Design tokens, color palette, overview |
| Layout | [navigation.md](references/navigation.md) | Header, sidebar, footer, breadcrumbs |
| Content | [content.md](references/content.md) | Hero, cards, testimonials, pricing |
| Forms | [forms.md](references/forms.md) | Inputs, buttons, complete forms |
| Data | [tables.md](references/tables.md) | Tables, pagination |
| Interactive | [interactive.md](references/interactive.md) | Modal, dropdown, tabs (Alpine.js) |
| Mobile | [mobile.md](references/mobile.md) | Bottom nav, mobile cards |
| States | [states.md](references/states.md) | Loading, empty, progress |

Use as inspiration. Modify freely or create new patterns.

## Context Analysis

### Understanding the Request
- Read user's prompt or document completely
- Extract the *intent*, not just the literal requirements
- Identify the emotional tone: professional, playful, minimal, rich?
- Consider the target audience

### Minimal Questions
Only ask when genuinely ambiguous:
- "Should I include [element] mentioned in the doc?"
- "The document describes two flows. Which is primary?"

Never ask about categories, styles, or structural choices — make creative decisions.

## File Naming & Storage

### Output Directory
All wireframes save to `wireframes/` in project root.

### Naming Convention
Generate kebab-case names from content (2-4 words):
- User profile page → `user-profile.html`
- Product checkout → `checkout-flow.html`
- Admin user list → `admin-users.html`

### Confirmation Flow
Suggest a name before saving:
> "I'll save this as `wireframes/user-profile.html`. Is this name okay?"

## Example

**User request:** "Create a simple landing page for a productivity app"

**Claude creates:** `wireframes/productivity-landing.html` with:
- Sticky header with logo and CTA
- Hero section with headline and app screenshot placeholder
- 3-column features grid
- Testimonial card
- CTA section
- Simple footer

## Your Task

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baslie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
