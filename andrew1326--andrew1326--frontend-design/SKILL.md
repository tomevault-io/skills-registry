---
name: frontend-design
description: Create distinctive, production-grade frontend interfaces with modern, minimalistic, and luxury design aesthetic. Use this skill when the user asks to build web components, pages, or applications. Use when this capability is needed.
metadata:
  author: andrew1326
---

This skill guides creation of distinctive, production-grade frontend interfaces with a **modern, minimalistic, and luxury** aesthetic. Every design should feel premium, refined, and intentional.

The user provides frontend requirements: a component, page, application, or interface to build. They may include context about the purpose, audience, or technical constraints.

## Core Design Philosophy

**MODERN**: Clean lines, contemporary patterns, cutting-edge but timeless. Think Apple, Stripe, Linear.

**MINIMALISTIC**: Every element earns its place. Generous whitespace. Restraint is sophistication. Remove until you can't.

**LUXURY**: Premium feel through subtle details. Quality over quantity. Refined typography. Deliberate color. Attention to micro-interactions.

## Design Principles

Before coding, commit to these principles:

1. **Less is More**: Strip away the unnecessary. If an element doesn't serve a clear purpose, remove it.
2. **Whitespace as Design**: Generous negative space creates breathing room and elegance.
3. **Typographic Hierarchy**: Let typography do the heavy lifting. One or two carefully chosen fonts.
4. **Subtle Motion**: Elegant transitions, not flashy animations. Think 200-300ms, ease-out curves.
5. **Monochromatic with Accent**: Dark backgrounds (deep blacks, navys) or clean whites with one accent color.
6. **Premium Details**: Subtle borders, refined shadows, glass effects, grain textures.

## Aesthetic Guidelines

### Typography
- **Display fonts**: DM Sans, Satoshi, Geist, Instrument Sans, Plus Jakarta Sans, Cabinet Grotesk
- **Body fonts**: Inter (sparingly), SF Pro, System UI for performance
- **Approach**: Large, bold headings with tight letter-spacing. Refined, readable body text.
- **Sizing**: Generous scale difference between heading and body (e.g., 4xl+ headings, base body)

### Color Palette
**Dark themes** (preferred for luxury feel):
- Background: #0a0a0f, #09090b, #0f0f14, #000000
- Surface: rgba(255,255,255,0.02-0.05), subtle glass effects
- Borders: rgba(255,255,255,0.05-0.1)
- Text: White for primary, gray-400/500 for secondary
- Accent: One saturated color (blue, purple, emerald) used sparingly

**Light themes** (when appropriate):
- Background: Pure white #ffffff or warm off-white
- Surface: Subtle grays for cards
- Text: Near-black #09090b
- Accent: Muted, sophisticated tones

### Layout & Spacing
- **Container**: Max-width 1200-1400px, generous horizontal padding (6-8 units)
- **Sections**: 24-32 units vertical padding
- **Grid**: Clean, asymmetric when purposeful
- **Cards**: Subtle borders, large border-radius (xl-3xl), minimal shadow

### Motion & Interaction
- **Transitions**: 200-400ms, ease-out or custom cubic-bezier
- **Hover states**: Subtle scale (1.02), opacity shifts, border color changes
- **Page loads**: Gentle fade-in or slide-up with staggered delays
- **Avoid**: Bouncy animations, excessive movement, attention-grabbing effects

### Visual Details
- **Gradients**: Subtle, often radial, for depth and atmosphere
- **Shadows**: Very subtle or none. When used, multi-layer for realism
- **Borders**: 1px, low opacity, creates definition without heaviness
- **Blur effects**: Backdrop blur for glass morphism when appropriate
- **Texture**: Subtle grain overlays add premium feel

## Anti-Patterns (NEVER do these)

- Colorful gradients on buttons
- Heavy drop shadows
- Excessive border-radius (full rounded on large elements)
- Multiple accent colors competing
- Busy backgrounds or patterns
- Generic stock illustrations
- Cluttered layouts with too many elements
- Animations that delay content visibility
- Cookie-cutter component libraries without customization

## Implementation Standards

Then implement working code (React/TypeScript for this project) that is:
- Production-grade and functional
- Visually refined and premium
- Cohesive with intentional restraint
- Meticulously polished in every detail

## Project-Specific Context

This is a **Freelancelyst** - a Next.js 15 (App Router) project for a web development agency. When building frontend components:

- Use React with TypeScript
- Use Tailwind CSS for styling
- Follow the existing component structure in `app/_components/`
- Place landing page components in `app/_components/landing/`
- Supports i18n with `lang` prop (TLocale type)
- Dark theme is the default (#0a0a0f background)
- Use lodash `get()` for safe dictionary access
- Accent color: Blue (#3b82f6 / blue-500) with purple secondary

### Design Reference
The current landing page follows evo-mobile.ru style:
- Deep black backgrounds
- Blue gradient accents
- Clean card layouts with subtle borders
- Generous spacing
- Professional, trustworthy feel

Remember: Luxury is in the restraint. Modern is in the simplicity. Quality is in the details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrew1326) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
