---
name: outmore-review
description: Run a comprehensive Outmore brand + code quality audit on the current project or specific files. Read-only. Use when this capability is needed.
metadata:
  author: outmore-living
---

# Outmore Code & Brand Review

You are performing a comprehensive audit. You do NOT modify any files — read-only analysis only.

## What to Audit
If `$ARGUMENTS` specifies files or directories, review those. Otherwise, scan the full `src/` directory.

## Audit Categories

### 1. Brand Compliance
Scan for violations of Outmore brand system:
- **Colors:** Are hardcoded hex values used instead of brand tokens? Look for raw `#373534`, `#f7f1e9`, `#F25431`, `#efefed` instead of token references.
- **Fonts:** Are `font-display`, `font-body`, `font-accent` used consistently? Any raw `font-family` declarations?
- **Buttons:** All using `rounded-full`? Proper padding (`px-6 py-3`)?
- **Cards:** Using `rounded-xl` or `rounded-2xl`?
- **Spacing:** Is whitespace generous? Any cramped layouts?
- **Theme:** Is the default warm off-white background (`#fcf9f5`) respected?

### 2. Accessibility
- Missing `aria-label` on icon-only buttons
- Missing `alt` on images
- Non-semantic HTML (div where button/a should be)
- Missing focus-visible styles
- Small touch targets (< 44px)
- Low contrast text

### 3. Security
- Hardcoded secrets or API keys in source
- Missing RLS on Supabase table references
- Unvalidated inputs in server actions
- Sensitive data in client components
- Missing auth checks on mutations

### 4. Performance
- Unnecessary `"use client"` directives
- Missing `next/image` usage for images
- `transition: all` instead of specific properties
- Large imports that could be tree-shaken
- Missing `prefers-reduced-motion` checks on animations

### 5. TypeScript Quality
- Any `any` types
- Missing interfaces/types for props
- Untyped function parameters
- Missing return types on exported functions

## Output Format

### Summary
One-paragraph overall assessment.

### Findings by Severity

**Critical** (must fix)
- List with file path, line number, issue, recommendation

**Important** (should fix)
- List with file path, line number, issue, recommendation

**Suggestions** (nice to have)
- List with file path, line number, issue, recommendation

### Score
Rate 1-10 on: Brand Compliance, Accessibility, Security, Performance, Code Quality

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/outmore-living) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
