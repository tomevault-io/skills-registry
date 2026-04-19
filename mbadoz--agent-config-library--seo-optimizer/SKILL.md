---
name: seo-optimizer
description: Ensures frontend components meet SEO and accessibility standards. Triggered when editing page.tsx, layout.tsx, or index.html files. Use when this capability is needed.
metadata:
  author: mbadoz
---

# Skill: SEO & Accessibility Optimizer

## Purpose
Automate the verification of critical SEO and Accessibility (A11y) standards during frontend development.

## Guidelines
- Follow the detailed requirements in [best-practices.md](references/best-practices.md).
- Use the [seo-report-template.md](assets/seo-report-template.md) when generating an audit report for the user.

## Core Checks
1. **Metadata**: Is title/description present and within length limits?
2. **Semantic HTML**: Proper heading hierarchy and landmark usage.
3. **Images**: All relevant images have descriptive alt text.
4. **Interactive**: Buttons/Links have accessible names.

## Usage
When a frontend file is modified, perform a quick scan based on the Checklist and propose fixes immediately.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbadoz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
