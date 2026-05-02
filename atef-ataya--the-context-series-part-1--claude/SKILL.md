---
name: code-review
description: Code review for Next.js and TypeScript projects. Use when asked to "review this code", "check this PR", "review my changes", "look at this component", or any code review request. Applies team standards for TypeScript types, React component patterns, error handling, accessibility, and performance. Use when this capability is needed.
metadata:
  author: atef-ataya
---

# Code Review

## Review Process

1. **Understand Intent** — What is this code trying to accomplish?
2. **Check Standards** — TypeScript types, component patterns, error handling
3. **Review Logic** — Bugs, edge cases, correctness
4. **Security Scan** — Common vulnerabilities (XSS, injection, exposed secrets)
5. **Synthesize Feedback** — Actionable recommendations

## Output Format

Always structure reviews as follows:

### Summary

One paragraph: What does this code do? Is it ready to merge?

### Critical Issues (Must Fix)

- Security vulnerabilities
- Logic errors / bugs
- Breaking changes

### Improvements (Should Fix)

- Missing TypeScript types
- Component structure issues
- Missing error handling

### Suggestions (Nice to Have)

- Performance optimizations
- Refactoring opportunities
- Better naming

## Tech Stack Context

This project uses:

- Next.js 16 (App Router)
- React 19
- TypeScript 5 (strict mode)
- Tailwind CSS 4
- Prisma ORM

## Component Conventions

- Functional components only
- PascalCase file names: `BlogCard.tsx`
- Props defined with `type` not `interface`
- Use `"use client"` directive only when needed
- Prefer Server Components by default

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atef-ataya) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
