---
name: frontend-design
description: > Use when this capability is needed.
metadata:
  author: ecocee
---

# Frontend Desgin

A design intelligence skill built around one principle: interfaces are used
by real people. Not by designers, not by developers — by people who are tired,
distracted, using their phone on a bus, or just trying to get something done.

Every decision in this skill — color, spacing, motion, copy, hierarchy —
is evaluated against that reality.

Compatible with: Claude, Claude Code, VS Code agent extensions, Cursor,
Windsurf, Antigravity, and any agent that supports the Agent Skills standard.

---

## Design Philosophy

Good design is invisible. When an interface is working, the user never thinks
about the interface. They think about their goal.

The job is not to make something that looks impressive in a Dribbble screenshot.
The job is to make something that gets out of the way and lets people do what
they came to do — with as little friction, confusion, and cognitive load as
possible.

Human-centered design asks three questions before any aesthetic decision:

1. Who is this person and what are they trying to do?
2. What is the clearest possible path to that outcome?
3. Does this design choice help or hinder that path?

If a beautiful animation slows someone down, remove it. If a minimalist layout
leaves someone confused about what to do next, add more. Design serves people.
People do not serve design.

---

## Process

### Step 1 — Understand the Human

Before opening a code editor or choosing a colour, answer these:

- **Who is the user?** Age, context, technical comfort, device, environment.
- **What are they trying to accomplish?** Not features — the real underlying goal.
- **What is their emotional state?** Stressed (banking, medical)? Playful (gaming, social)? Focused (developer tool)? Exploratory (e-commerce)?
- **Where might they struggle?** Identify the one step most likely to cause drop-off or confusion.

The answer to these questions determines everything else: font size, contrast ratio,
the number of steps in a form, the weight of a primary button.

### Step 2 — Define the Hierarchy

Before any styling, establish what the user must see, understand, and do — in order.

Every screen has exactly one primary action. Everything else either supports
that action or gets out of the way. If you cannot name the primary action,
the design does not have enough direction yet.

Hierarchy checklist:
- One thing draws the eye first (size, weight, or colour contrast)
- The primary action is the most visually prominent interactive element
- Supporting information is visually quieter than primary information
- Destructive or irreversible actions are not the most prominent thing on screen

### Step 3 — Choose Visual Direction

Match the visual direction to the emotional context of the product, not to trends.

| Emotional Context | Visual Direction | Avoid |
|---|---|---|
| Trust and safety (finance, medical, legal) | Clean, spacious, high contrast, conservative type | Dark mode, playful fonts, heavy gradients |
| Creativity and expression (design tools, art, music) | Rich colour, expressive type, generous motion | Sterile layouts, tight spacing |
| Productivity and focus (developer tools, SaaS) | Neutral palette, dense information, monospace accents | Decorative elements that compete for attention |
| Warmth and care (wellness, community, education) | Warm palette, rounded forms, humanist type | Cold blues, sharp edges, clinical spacing |
| Excitement and aspiration (consumer, gaming, fashion) | Bold contrast, expressive type, energetic motion | Timid colour, overly safe layouts |
| Commerce and conversion (e-commerce, marketing) | Clear hierarchy, high-contrast CTA, trust signals | Cluttered layouts, competing calls to action |

### Step 4 — Design System Decisions

Define these before writing any code. Changing them later is expensive.

**Typography**
Pick two fonts: one for display/headings, one for body. They should have
complementary personalities — one with character, one with clarity.
The body font carries most of the reading load. It must be legible at 16px,
comfortable at 1.5 line height, and work at 65 characters per line.

Never use more than two font families. Every additional font is noise.

**Colour**
Start with one dominant colour that reflects the emotional direction.
Add one accent for interactive elements and calls to action.
Add a neutral scale (5–7 steps from near-white to near-black) for text,
borders, backgrounds, and surfaces.
That is the full palette. Add more only when you have a specific reason.

Define all colours as CSS custom properties. Never hardcode hex values
in component styles.

**Spacing**
Use a consistent spacing scale (4px base: 4, 8, 12, 16, 24, 32, 48, 64, 96).
Inconsistent spacing is the most common reason a design looks amateurish.
Whitespace is not empty — it is rest for the eye and signal for hierarchy.

**Elevation and Depth**
Use shadow sparingly — one or two elevation levels maximum.
Shadow signals "this element is above the surface". Overuse makes everything
feel equally elevated and nothing feel important.

---

## Core Design Rules

### Typography (Human Legibility First)

- Body text minimum 16px on all screen sizes — never smaller
- Line height 1.5 to 1.75 for body text; tighter (1.2 to 1.4) for headings
- Line length 60 to 75 characters per line for comfortable reading
- Use `text-wrap: balance` on all headings to prevent orphaned words
- `font-variant-numeric: tabular-nums` on any column of numbers or prices
- Ellipsis character `…` not three periods `...`
- Never use more than two typefaces in a single interface

Font pairing by emotional direction:

| Direction | Display | Body |
|---|---|---|
| Trust and authority | Playfair Display, Lora | Source Serif 4, Georgia |
| Productivity and precision | JetBrains Mono, IBM Plex Mono | IBM Plex Sans, Inter |
| Warmth and community | Nunito, Quicksand | Lato, Open Sans |
| Creativity and expression | Fraunces, Cabinet Grotesk | DM Sans, Outfit |
| Premium and luxury | Cormorant Garamond, Canela | Suisse Int'l, Aktiv Grotesk |
| Playful and youthful | Paytone One, Righteous | Nunito, Poppins |
| Editorial and media | Libre Baskerville, Spectral | Merriweather, Source Sans 3 |

Fonts to avoid (overused to the point of being invisible): Inter as a display
font, Roboto as a brand font, Arial as anything intentional, Space Grotesk
as a "distinctive" choice. These read as defaults, not decisions.

### Colour (Meaning and Contrast)

- Normal text: minimum 4.5:1 contrast ratio against background
- Large text (18px+ or 14px+ bold): minimum 3:1
- Interactive element focus states: minimum 3:1 against adjacent colours
- Never use colour as the sole indicator of meaning (also use shape or text)
- Warm neutrals (zinc, stone, slate-warm) read as more human than cool greys

Avoid:
- Purple-on-white gradients — the default AI aesthetic
- Saturated neon on dark backgrounds as a primary surface treatment
- More than four distinct hues in one interface
- Equal visual weight across all colours — one must dominate

### Spacing and Layout

- Consistent spacing scale — never arbitrary pixel values
- Whitespace between sections should be larger than whitespace within sections
- Cards should have equal padding on all sides (or explicit top/bottom vs left/right intent)
- Floating navbars need `top-4 left-4 right-4` inset — never flush to the viewport edge
- Fixed navbars need matching padding-top on the page content below
- Mobile minimum touch target: 44x44px — every button, link, and icon

### Interaction and Motion

- Micro-interactions: 150 to 300ms — long enough to feel smooth, short enough to feel fast
- Page transitions: 200 to 400ms
- Never `transition: all` — list properties explicitly
- Animate only `transform` and `opacity` — never `width`, `height`, `top`, `left`
- Always provide a `prefers-reduced-motion` alternative that removes motion
- One primary animation per page — the moment of highest emotional impact
- Hover states must change something visible — colour, shadow, or position — never nothing

Loading states:
- Under 300ms: no indicator needed
- 300ms to 1 second: subtle spinner or shimmer
- Over 1 second: skeleton screen that matches the content layout
- Never leave a user staring at a blank space without feedback

### Forms (Where Most Frustration Lives)

- Every input has a visible label — not a placeholder that disappears on type
- Placeholders show example input format, end with `…`, never replace labels
- Inline error messages appear next to the field that caused them
- Error messages say what is wrong AND how to fix it ("Must be at least 8 characters" not "Invalid password")
- Submit button stays enabled until the request starts — then shows a spinner
- Focus first error field on failed submission — never make the user hunt
- Do not block paste on any input — this breaks password managers
- Group related fields with visual proximity, not just logical order

### Accessibility (Non-Negotiable)

- All icon-only buttons require `aria-label`
- All form inputs require associated `<label>` elements
- All images require `alt` text (or `alt=""` for decorative images)
- Decorative icons require `aria-hidden="true"`
- Tab order must match visual reading order
- Use `<button>` for actions, `<a>` for navigation — never `<div onClick>`
- Async updates (toasts, validation) need `aria-live="polite"`
- Skip link to main content for keyboard users
- Never disable zoom (`user-scalable=no` is an accessibility violation)

---

## Component Standards

### Buttons

- Primary button: highest contrast, most visually prominent — one per view
- Secondary button: lower visual weight, supporting action
- Destructive button: red tone, requires confirmation for irreversible actions
- Ghost / text button: for low-priority actions only
- Loading state: disable + show spinner — never two clicks on async actions
- All buttons need `cursor-pointer`, `focus-visible` ring, and hover state

### Navigation

- Floating navbar: `top-4 left-4 right-4` with `backdrop-blur` and border
- Sticky navbar: `position: sticky; top: 0` with enough z-index to clear content
- Mobile: hamburger or bottom navigation — never a desktop nav jammed into mobile
- Active state: clearly different from inactive — not just a colour shade shift

### Cards

- Consistent padding (usually 24px or 32px)
- Hover state if the card is interactive — shadow lift or border colour change
- No hover state if the card is purely informational — false affordance confuses
- `cursor-pointer` on interactive cards only

### Modals and Drawers

- `overscroll-behavior: contain` to prevent background scroll
- Focus trap inside modal while open
- Close on backdrop click and Escape key
- Destructive actions inside modals need a second confirmation step
- Mobile: drawers from the bottom edge, not centred modals

### Tables

- Sticky header on long tables
- `font-variant-numeric: tabular-nums` on all numeric columns
- Alternating row background or clear row borders — not both
- Empty state: message and action, never a blank table frame

---

## Anti-Patterns (Always Flag)

| Anti-Pattern | Why It Fails Humans |
|---|---|
| Emoji used as UI icons | Inconsistent rendering across OS, no sizing control, inaccessible |
| Placeholder as the only label | Label disappears when user starts typing — causes errors and re-entry |
| Colour as the only error indicator | Fails for colour-blind users |
| Disabled submit until all fields valid | Prevents user from discovering which field is wrong |
| Animation on every element | Creates noise, not delight — reduces perceived performance |
| `transition: all` | Causes unintended transitions on layout properties |
| `outline: none` without replacement | Removes all keyboard focus visibility |
| `<div onClick>` or `<span onClick>` | Not keyboard accessible, no screen reader semantics |
| Hover-only interactions on mobile | Touch devices have no hover state |
| Text below 16px on mobile | Legibility degrades; causes zoom — disrupts layout |
| No loading state on async actions | User does not know if their action registered |
| Success/error as a colour change only | Fails colour-blind users; toast disappears too fast |
| Deep OFFSET pagination | Gets slower with every page; use keyset pagination |
| Error message without a fix | "Invalid email" is less useful than "Email must include @" |
| Inconsistent spacing | The single most common reason a UI looks unpolished |

---

## Pre-Delivery Checklist

Complete this before handing off or deploying any interface.

**Typography**
- [ ] Body text 16px minimum on all breakpoints
- [ ] Line height 1.5 or above for body copy
- [ ] No more than two font families
- [ ] Headings use `text-wrap: balance`
- [ ] Number columns use `tabular-nums`

**Colour and Contrast**
- [ ] Normal text passes 4.5:1 contrast ratio
- [ ] No purple-on-white gradient as primary aesthetic
- [ ] Colour is not the sole indicator of state or error
- [ ] Both light and dark modes tested (if applicable)

**Interaction**
- [ ] All clickable elements have `cursor-pointer`
- [ ] All interactive elements have visible hover and focus states
- [ ] Transitions are 150 to 300ms and list properties explicitly
- [ ] `prefers-reduced-motion` respected

**Forms**
- [ ] Every input has a visible label (not just a placeholder)
- [ ] Error messages say what is wrong and how to fix it
- [ ] Paste is not blocked on any input
- [ ] Submit shows loading state during async operation

**Accessibility**
- [ ] Icon-only buttons have `aria-label`
- [ ] All images have `alt` text
- [ ] Semantic HTML used (`<button>`, `<a>`, `<label>`, `<nav>`)
- [ ] Tab order matches visual order
- [ ] No `user-scalable=no`

**Layout**
- [ ] No content hidden behind fixed navbars
- [ ] No horizontal scroll on 375px viewport
- [ ] Touch targets minimum 44x44px
- [ ] Consistent spacing scale throughout

**Loading and Empty States**
- [ ] Async operations have a loading indicator
- [ ] Empty arrays and empty strings have a defined UI state
- [ ] Skeleton screens match the layout of loaded content

---

## Quick Reference by Stack

| Stack | Key Rules |
|---|---|
| HTML/Tailwind | Use `prose` for body text, `aspect-ratio` for media, `container mx-auto px-4` for layout |
| React | Prefer CSS modules or Tailwind over inline styles; no layout reads in render |
| Next.js | Use `next/image` with explicit width/height; `next/font` for zero-layout-shift fonts |
| Vue | Scoped styles; `v-memo` for expensive list items; `Transition` component with CSS |
| Svelte | `transition:` directives respect `prefers-reduced-motion` automatically |
| React Native | `accessibilityLabel` on all touchable elements; `hitSlop` for small targets |
| SwiftUI | `.accessibilityLabel()` on all Image views; `.dynamicTypeSize` support |
| Flutter | `Semantics` widget on custom widgets; `MediaQuery.boldText` for accessibility |
| shadcn/ui | Use `cn()` for conditional classes; do not override Radix accessibility props |

---

## References

- Web Content Accessibility Guidelines (WCAG) 2.2: https://www.w3.org/TR/WCAG22/
- Google Fonts: https://fonts.google.com
- Tailwind CSS documentation: https://tailwindcss.com/docs
- Radix UI primitives: https://www.radix-ui.com
- Heroicons: https://heroicons.com
- Lucide Icons: https://lucide.dev
- Simple Icons (brand logos): https://simpleicons.org
- Contrast checker: https://webaim.org/resources/contrastchecker/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ecocee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
