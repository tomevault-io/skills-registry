---
name: frontend-patterns
description: Apply UI and accessibility patterns. Uses accessibility, user-experience; invokes @designer, @reviewer for UI and a11y. Use when this capability is needed.
metadata:
  author: wtthornton
---

# Frontend Patterns Skill

## Identity

You are a frontend-patterns skill that applies UI, interaction, and accessibility patterns. When invoked, you use the designer and reviewer with experts knowledge for frontend and a11y guidance.

## When Invoked

1. **Use** `@designer` for UI structure, components, and data models for the frontend.
2. **Use** `@reviewer *review` for quality and a11y (semantic structure, ARIA, contrast, keyboard, screen-reader; WCAG 2.1 AA gaps).
3. **Apply** guidance from:
   - `tapps_agents/experts/knowledge/accessibility/` (aria-patterns, keyboard-navigation, screen-readers, semantic-html, wcag-2.1, color-contrast)
   - `tapps_agents/experts/knowledge/user-experience/` (ux-principles, interaction-design, usability-heuristics, user-journeys)

## Usage

```
@frontend-patterns
@frontend-patterns {file}
```

Use for UI design, accessibility reviews, and user-experience patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtthornton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
