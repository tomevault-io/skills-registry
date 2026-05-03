---
name: genesis-site-design
description: Design and build Genesis' marketing website as a B2B SaaS product that can also scale into an international "neo-consultancy" (product + services). This skill is for marketing sites (not dashboards). It prioritises layout systems, information architecture, proof, and technical excellence over component demos. Use when this capability is needed.
metadata:
  author: ashgar99
---

# Genesis Site Design

Genesis is a marketing-intelligence B2B SaaS platform that must look credible to enterprise buyers, investors, and future partners; whilst also signalling hands-on delivery capability (a consultancy/agency layer) without feeling like a traditional agency site.

This skill exists because most coding outputs nail components but fail the thing buyers actually feel first: **layout, hierarchy, and narrative**.

## Scope

**Use for**
- Marketing website pages (home, product, platform, pricing, resources, case studies, company, careers, legal)
- Section systems, page templates, content models, and sitewide design tokens
- Responsive layout, motion, accessibility, performance, and SEO

**Not for**
- Product UI (dashboards, admin panels, in-app flows). Use `/interface-design`.

---

# Success Criteria

A Genesis page is "professional" when it:
- Reads like a confident, specific product; not a general agency
- Demonstrates proof (numbers, logos, case studies, methodology, security posture)
- Feels intentional on mobile and desktop (type scale, spacing rhythm, grid)
- Loads fast, passes Lighthouse, and is accessible by default

If another model could produce the same site from a generic prompt, the work failed.

---

# Multi-Agent Workflow (Required)

You will run **6 specialised agents** (as internal roles) and then synthesise.

## Agent 1: Competitive & Reference Analyst
**Goal:** Extract patterns from high-performing B2B SaaS + digital consultancies.
**Must produce:**
- 12 references split into two buckets:
  - **SaaS product excellence** (e.g., Stripe, Linear, Vercel, Datadog, Notion, Webflow, Figma)
  - **Consultancy/neo-consultancy excellence** (e.g., Thoughtworks, Accenture Song, Deloitte Digital, BCG X, frog, AKQA, Work & Co)
- For each reference: 3 "borrowable" layout moves + 1 thing to avoid.

## Agent 2: Information Architect
**Goal:** Decide the site map, page purposes, and content model.
**Must produce:**
- Site map (top-level + 1 level deep)
- A reusable **section inventory** (15–25 sections) with intent, inputs, and variants
- A "proof ladder": what evidence appears at each scroll depth (above the fold → footer)

## Agent 3: Brand & Visual System Designer
**Goal:** Create the visual language so Genesis is memorable and not template-ish.
**Must produce:**
- Type system (display + body + mono; with fallbacks)
- Colour system (tokens, light/dark, semantic states)
- Layout system (grid, container widths, spacing scale using **rem** + occasional px for hairlines)
- A single "signature motif" (unique to Genesis) that appears in multiple places

## Agent 4: Copy & Narrative Editor
**Goal:** Make the site read like a product that ships outcomes.
**Must produce:**
- One-sentence positioning
- Hero headline + subhead + 2 CTA options
- 3 message pillars with proof hooks
- "Neo-consultancy without agency cringe" rules (ban vague claims; prefer concrete mechanisms)

## Agent 5: Interaction & Motion Designer
**Goal:** Motion that signals craft, not gimmicks.
**Must produce:**
- Page-load choreography (stagger; <600ms total feel)
- Scroll-linked moments (sparingly)
- Hover/focus micro-interactions
- Reduced-motion plan (prefers-reduced-motion compliance)

## Agent 6: Frontend Systems Engineer
**Goal:** Make it production-grade.
**Must produce:**
- Stack recommendation (default: Next.js + TS + Tailwind or CSS Modules + MDX/content layer)
- Component architecture (section-first, not component-first)
- Performance plan (image strategy, font loading, code splitting)
- Accessibility checklist (WCAG basics, keyboard, focus, contrast)

---

# The Genesis "Dual Identity" Rule

Genesis must look like:
1) **A platform** that compounds intelligence (SaaS credibility)
2) **A delivery engine** that applies that intelligence (consultancy credibility)

Do not mash them together. Use a **two-lane narrative**:

- Lane A (Product): platform capabilities, data, workflows, integrations, security
- Lane B (Delivery): programmes, enablement, onboarding, playbooks, outcomes

Each lane gets:
- Dedicated sections
- Dedicated pages
- Dedicated proof (case studies split by "platform-led" vs "programme-led")

---

# Page Templates (Minimum Set)

1. **Home** – positioning, proof, platform overview, outcomes, CTA
2. **Platform** – how Genesis works (data → intelligence → action), architecture diagram, integrations
3. **Solutions** – by role (Marketing Lead, Growth, RevOps, Founder) and/or industry
4. **Case Studies** – problem, approach, artefacts, results, timeline, quotes
5. **Pricing** – simple tiers + enterprise contact; show what's included; procurement-friendly
6. **Resources** – blog, reports, benchmarks, templates (lead capture without spam energy)
7. **Company** – mission, values, operating principles, security/compliance stance
8. **Careers** – talent brand, working style, benefits, interview process
9. **Legal** – privacy, cookies, terms (boring but immaculate)

---

# Section System (Design for Reuse)

Design pages as compositions of high-quality **sections** (not random components).
Every section must declare:
- Purpose (what decision it helps the buyer make)
- Inputs (copy, stats, logos, screenshots)
- Variants (short/long, light/dark, media-left/right)
- Responsive behaviour
- Accessibility notes

Examples of required sections:
- Hero (two CTAs + trust strip)
- "How it works" (3–5 steps with a visual narrative)
- Product screenshots with annotated callouts
- Methodology / framework (Genesis' distinctive model)
- Proof grid (metrics, logos, certifications, press)
- Case study teaser cards
- Comparison table (Genesis vs status quo)
- Security & compliance (plain language, not legalese)
- Integrations strip
- Founder note / vision (optional; keep factual)
- Primary CTA section (no generic "Let's talk")

---

# Professional Aesthetic: What to Copy from the Japanese References

From strong Japanese B2B agencies/consultancies, pull these patterns:
- Clear top navigation with restrained density
- Above-the-fold clarity: what they do + who for + proof
- Case studies treated as first-class (not "portfolio vibes")
- Big-number evidence blocks (volume, years, outcomes)
- Calm spacing, disciplined grids, and careful typography
- Frequent but tasteful CTAs (資料請求 / お問い合わせ patterns translate well)

Avoid:
- Overly decorative hero art that says nothing
- Stock-photo "smiling meeting" clichés
- Endless service tiles with identical blurbs
- Random border radii, shadow styles, and spacing values

---

# Layout-First Rules (Non-Negotiable)

1. **Start with page maps**: draw boxes before styling.
2. Establish **grid + spacing rhythm** (rem-based): e.g., 0.25rem steps or a 4px equivalent.
3. Define **content widths**: readable measure for text; wider for visuals.
4. Only then design components to fit the layout system.

If layout isn't decided, do not code.

---

# Implementation Rules

## Units & Responsiveness
- Use `rem` for spacing and type; allow px only for hairlines, icons, and exact media borders.
- Mobile-first; no desktop-first "shrink to fit".
- Use fluid type (`clamp`) for hero and section headings.

## Typography
- Two-font system: expressive display + disciplined body.
- One monospace for data/metrics.
- Tight tracking on headings; generous line-height on body.

## Colour
- One dominant brand hue + one sharp accent; everything else is structural neutrals.
- Semantic colours have purpose (success, warning, destructive).
- Dark mode is not an afterthought; it is a first-class theme.

## Motion
- Prefer CSS for simple interactions; use a motion library only when it adds clear value.
- Respect reduced motion.
- One or two "hero moments" beat constant animation.

## Proof & Trust
B2B buyers need:
- Logos (curated, not a wall)
- Metrics with context (timeframe, baseline)
- Named frameworks / methodology (not buzzwords)
- Security posture page (SOC2 roadmap, data handling, SSO, RBAC — say only what's true)

## Performance
- Use modern image formats; responsive sizes; lazy-load below the fold.
- Self-host fonts; preconnect sparingly.
- Avoid heavy third-party scripts; measure each one.

---

# Output Requirements (What you must return)

1. **Reference Digest**: the 12-site reference list with "borrow / avoid".
2. **Site Map + Section Inventory** (with variants).
3. **Design System Tokens**: spacing, type scale, colours, radii, shadows.
4. **One page fully specified** (Home) as a layout blueprint:
   - Section order
   - Copy placeholders
   - Proof placement
   - Component requirements
5. **Only then**: production code (Next.js preferred) implementing the section system.

---

# Commands

- `/genesis-site-design:references` — Produce the 12-site reference digest
- `/genesis-site-design:sitemap` — Produce site map + section inventory
- `/genesis-site-design:tokens` — Produce design tokens (CSS variables + Tailwind config if relevant)
- `/genesis-site-design:blueprint home` — Layout blueprint for Home page
- `/genesis-site-design:build` — Generate production code from approved blueprint
- `/genesis-site-design:audit` — Check implementation against tokens, layout, a11y, performance

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashgar99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
