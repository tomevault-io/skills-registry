---
name: design-review
description: | Use when this capability is needed.
metadata:
  author: dayoumin
---

# UI/UX Design Review Skill

Senior UI/UX design reviewer for SaaS applications (Stripe, Airbnb, Linear quality).

## When to Activate

- After `.tsx`, `.css`, `.scss` file modifications
- Layout or style related questions
- User requests: "디자인 확인", "UI 검토", "design review", "check layout"

## Quick Checklist

### 1. Visual Consistency
- [ ] 8px spacing system (p-2, p-4, p-6, p-8)
- [ ] Typography hierarchy clear
- [ ] Color consistency (Primary, Muted, Destructive, Accent)
- [ ] Visual hierarchy appropriate

### 2. Responsive Design
- [ ] Mobile (<768px): single column, touch-friendly (min 44x44px)
- [ ] Tablet (768-1024px): adaptive layout
- [ ] Desktop (>1024px): full layout with sidebar

### 3. Accessibility (WCAG 2.1 AA)
- [ ] Color contrast 4.5:1+
- [ ] Keyboard navigation support
- [ ] Focus states visible (`focus-visible:ring-2`)
- [ ] Semantic HTML (`<header>`, `<nav>`, `<main>`, `<footer>`)
- [ ] Proper `aria-label`, `role` usage

### 4. Interactions
- [ ] hover/focus/active states defined
- [ ] Loading feedback (skeleton, spinner)
- [ ] Error handling (toast, NOT `alert()`)
- [ ] Animations 150-300ms

## Issue Priority

| Level | Description |
|-------|-------------|
| **[Blocker]** | Release blocking - broken layout, accessibility violation |
| **[High]** | Significant UX impact - small touch targets, no keyboard access |
| **[Medium]** | Visual inconsistency - spacing, color mismatch |
| **[Nitpick]** | Suggestions - code cleanup, minor tweaks |

## Report Format

```markdown
# 🎨 Design Review

## Summary
- **Scope**: [file list]
- **Score**: X/10

## Issues
### [Priority] Issue Title
- **Location**: file:line
- **Problem**: description
- **Suggestion**: fix direction

## Positive Observations
[Well-implemented patterns]

## Recommendations
[Improvement suggestions]
```

## Project Context

- **UI Framework**: shadcn/ui + Tailwind CSS v4
- **Icons**: Lucide React
- **Animations**: Framer Motion
- **Container**: `max-w-6xl mx-auto w-full px-6`
- **Layout**: Header + Sidebar + Main content

See [CLAUDE.md](../../../CLAUDE.md) for detailed design guidelines.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dayoumin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
