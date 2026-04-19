---
name: shadcn-ui-builder
description: Builds production-ready, SEO-optimized, performance-focused, WCAG 2.1 AA–compliant landing pages using Next.js, shadcn/ui, Tailwind CSS, TypeScript, and Framer Motion.
metadata:
  author: olewandowski1
---

# Shadcn Landing Page UI Builder Skill

This skill is a **pure implementation step**.

It builds the landing page strictly according to an approved
**`IMPLEMENTATION-PLAN.md`** file.

The ONLY allowed verification at this stage is ensuring that the project
**builds and runs without errors**.

No UI testing, QA, or behavioral validation is performed here.

## Invocation Rule (MANDATORY)

- This skill MUST be invoked only after **`shadcn-component-research`**
- The input to this skill MUST be: `IMPLEMENTATION-PLAN.md`
- `IMPLEMENTATION-PLAN.md` is the **single source of truth**
- If anything is unclear:
- STOP
- Report the issue
- Do NOT guess

## Mandatory Pre-Step: Repository Analysis

Before writing any code, the builder MUST:

1. Read the repository template
2. Understand:
 - Folder structure
 - Component conventions
 - Styling and theming approach
3. Respect all existing patterns

❗ No new architectural patterns or directories may be introduced.

## Assumed Tech Stack

- Next.js (App Router)
- TypeScript
- shadcn/ui
- Tailwind CSS
- Framer Motion
- SEO via Next.js metadata APIs

## Strict Project Structure Rules

### Custom Components

- ALL custom components MUST live in: `@/components`
- No additional directories may be created

### shadcn/ui Components

- MUST live in: `@/components/ui`
- Do NOT modify internals unless absolutely unavoidable

## Mandatory Framework APIs

- **ALL images MUST use `next/image`**
- **ALL internal navigation MUST use `next/link`**
- Raw `<img>` and `<a>` tags for internal navigation are NOT allowed

## Theme Rules

- Use the **preferred theme** defined in `IMPLEMENTATION-PLAN.md`
- Theme switching may be enabled or disabled per plan
- No system-based or automatic switching
- Colors via shadcn CSS variables
- WCAG 2.1 AA contrast required

## Navbar Requirements (MANDATORY)

Every landing page MUST include a clearly defined navbar.

The navbar MUST:

- Be **sticky**
- Contain all primary navigation items
- Reflect the section structure from `IMPLEMENTATION-PLAN.md`
- Support scroll-based navigation when applicable

### Accessibility & Responsiveness

- Semantic `nav`
- Keyboard accessible
- Visible focus states
- Mobile menu required (accessible, no layout shift)

## Hero Section Rule (MANDATORY)

- Semantic `section`
- Full viewport height (`min-h-screen`)
- First section in `<main>`
- Contains the **only H1**
- Visible immediately on load

## Performance Is a Feature (MANDATORY)

- Optimize all images
- Avoid heavy hero media
- Lazy-load non-critical sections
- Prevent CLS
- Keep Framer Motion subtle and short

If performance and visuals conflict, performance wins.

## Required Assets

### Favicon
- Lucide React icon
- Primary color background
- Static asset

### Open Graph Image
- Minimal
- Project name focus
- High contrast
- Static asset

## Motion Rules

- Framer Motion only
- No layout-shifting animations
- Respect `prefers-reduced-motion`

## Accessibility (MANDATORY)

- WCAG 2.1 AA minimum
- Keyboard navigation
- Visible focus
- Correct labels
- Semantic HTML

## Build & Run Verification (MANDATORY)

The ONLY verification allowed during this skill is:

1. Running: `npm run build`
2. Running: `npm run start`

Rules:
- Both commands MUST complete without errors
- Fix ONLY issues that prevent build or dev server from running
- Do NOT treat this as QA or UI testing

## Output Expectations

- Production-ready TypeScript code
- Correct folder placement
- Assets included
- Confirmation that:
- `npm run build` succeeds
- `npm run dev` starts without errors

## What NOT to Do

- Do NOT perform UI or UX testing
- Do NOT add tests
- Do NOT redesign sections
- Do NOT introduce new dependencies
- Do NOT reinterpret requirements
- Do NOT skip build or dev verification

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/olewandowski1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
