---
name: accessibility-checker
description: Content accessibility audit — check readability for screen readers, color contrast for visuals, mobile-first formatting, and inclusive language. Reaching more people = more reach. Accessibility is growth strategy, not just ethics. Use when this capability is needed.
metadata:
  author: Gaura3560
---

# Accessibility Checker

Content that everyone can consume reaches more people. Accessibility IS growth.

## When to Activate

- User says `/accessibility {file}` or `/a11y {file}`
- User asks "is this accessible?"
- Auto-suggested by pre-publish for visual content

## Commands

### `/a11y {file}` — Full accessibility audit
### `/a11y visual {image}` — Check image accessibility
### `/a11y mobile {file}` — Mobile readability check

## Checks

```
Accessibility Audit: "{title}"
============================================

Text Accessibility:
  [ ] Heading hierarchy (H1→H2→H3, no skips): {pass/fail}
  [ ] Paragraph length (≤3 sentences for mobile): {pass/fail}
  [ ] Line length (≤40 chars for Japanese mobile): {pass/fail}
  [ ] Jargon explained: {pass/fail}
  [ ] Abbreviations expanded on first use: {pass/fail}

Visual Accessibility:
  [ ] Image alt text / description: {present/missing}
  [ ] Color contrast ratio (text on bg): {ratio} (min 4.5:1)
  [ ] Text readable without images: {yes/no}
  [ ] No color-only information: {pass/fail}

Mobile Accessibility:
  [ ] Content readable on small screen: {pass/fail}
  [ ] Links/CTAs are tap-friendly: {pass/fail}
  [ ] No horizontal scrolling needed: {pass/fail}
  [ ] Key info above the fold: {pass/fail}

Inclusive Language:
  [ ] Gender-neutral where possible: {pass/fail}
  [ ] No ableist language: {pass/fail}
  [ ] Age-inclusive references: {pass/fail}

Score: {passed}/{total}
Status: {ACCESSIBLE / NEEDS FIXES}

============================================
```

## Quality Gate

- [ ] Checks cover text, visual, mobile, and language
- [ ] Fixes are specific and easy to implement
- [ ] Color contrast measured against WCAG standards
- [ ] Mobile checks reflect actual small-screen experience

---
> Source: [Gaura3560/content-autopilot](https://github.com/Gaura3560/content-autopilot) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
