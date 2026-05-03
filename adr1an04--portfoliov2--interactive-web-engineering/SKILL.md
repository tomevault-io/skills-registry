---
name: interactive-web-engineering
description: Engineer immersive, SEO-friendly web interfaces that stay fast under real constraints. Use when building or refactoring frontend experiences, especially Next.js App Router and GSAP work, that require deliberate UI hierarchy, motion orchestration, server/client component boundaries, accessibility, and Core Web Vitals protection. Use when this capability is needed.
metadata:
  author: adr1an04
---

# Interactive Web Engineering

## Overview

Build interactive web experiences that feel intentional and distinctive without sacrificing maintainability, accessibility, or speed. Optimize structure first, then motion, then decoration while enforcing 60fps and Core Web Vitals guardrails.

## Execute Workflow

1. Start with one concrete feature instead of global shell layout.
2. Hold color until hierarchy, contrast, and spacing are correct in grayscale.
3. De-emphasize non-critical elements before amplifying primary actions.
4. Define typography, spacing, and color systems early to reduce random decisions.
5. Keep Next.js components server-first and push `'use client'` to interactive leaves.
6. Use motion only where it improves clarity or narrative continuity.
7. Animate `transform` and `opacity`; avoid layout-triggering properties.
8. Validate CLS, LCP, INP, reduced-motion behavior, and mobile layout before handoff.

## Apply Next.js Architecture Rules

- Keep data fetching, secret access, and static SEO markup in server components.
- Move `useState`, `useEffect`, event handlers, and browser API usage to client components.
- Use `next/image` sizing (`fill` plus `sizes`, or explicit `width`/`height`) to prevent CLS.
- Use `next/font` for self-hosted font delivery and stable text metrics.
- Lazy-load non-critical animation sections below the fold with `next/dynamic`.

## Apply GSAP Motion Rules

- Register and scope animations through `useGSAP` to guarantee cleanup in React Strict Mode.
- Wrap late-bound event callbacks in `contextSafe()` to keep cleanup reliable.
- Use `gsap.matchMedia()` to disable or simplify expensive motion on small screens.
- Honor `prefers-reduced-motion: reduce` with minimal or no animation timelines.
- Use `ScrollTrigger` pinning and scrub only when content comprehension improves.

## Apply Performance Guardrails

- Protect the rendering pipeline: avoid animating `width`, `height`, `margin`, `top`, or `left`.
- Reserve heavy `filter` effects for rare accents, not continuous scroll-linked motion.
- Keep hero/LCP imagery static and fast to render.
- Set animation initial states early to prevent flash and unexpected layout shifts.

## Load Detailed Reference

- Read `references/master-class-interactive-web-engineering.md` when detailed guidance is needed for UI hierarchy, color systems, typography mechanics, App Router boundaries, GSAP orchestration, and performance strategy.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adr1an04) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
