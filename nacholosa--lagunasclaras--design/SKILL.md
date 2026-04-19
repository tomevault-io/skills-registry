---
name: design
description: When the use wants to design, create or rework any page or component. Use when this capability is needed.
metadata:
  author: nacholosa
---

# DESIGN.md

## Lagunas Claras – Design System

This document defines the visual and design rules for the Lagunas Claras website.
All UI and visual decisions must follow this system.

---

## Brand Personality

- Professional
- Technical
- Natural
- Trustworthy
- Calm

Avoid:

- Playful or cartoonish styles
- Over-saturated greens
- “Eco cliché” visuals

---

## Color System

Colors are defined using semantic tokens.
Do NOT use raw HEX or RGB values in components.

### Core Tokens

- `primary` → Brand / CTAs
- `secondary` → Support elements
- `accent` → Highlights
- `background` → Main background
- `foreground` → Text
- `muted` → UI surfaces, borders

All colors are implemented via CSS variables and Tailwind tokens.

---

## Typography

- Sans-serif, modern, readable
- Clear hierarchy
- No decorative fonts

Usage:

- Headings → strong but calm
- Body → neutral and readable
- Avoid excessive font weights

---

## Spacing & Layout

- Prefer generous white space
- Avoid dense layouts
- Use consistent vertical rhythm
- Desktop-first approach

---

## Components

- Built on shadcn/ui primitives
- Extended only when necessary
- No custom styling that breaks tokens

Buttons:

- Primary → main actions
- Secondary → supportive actions
- Avoid more than 2 button styles per view

---

## Sections & Composition

- Clear section separation
- Visual hierarchy > decoration
- Images must feel real, not stocky

Hero sections:

- Strong headline
- Subtle background treatment
- Optional soft parallax

---

## Motion & Animation

- Subtle and purposeful
- Used to guide attention, not entertain
- Avoid heavy or continuous animations

Allowed:

- Fade-in
- Slide-up
- Slight parallax

---

## Accessibility

- Sufficient contrast
- Readable font sizes
- Clear interactive states

---

## Final Rule

If a design decision is unclear:

- Choose clarity over creativity
- Choose calm over intensity
- Choose consistency over novelty

## Final Rule

If a design decision is unclear:

- Choose clarity over creativity
- Choose calm over intensity
- Choose consistency over novelty

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nacholosa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
