---
name: figma-prompt
description: > Use when this capability is needed.
metadata:
  author: mjrtl
---

# Generate Figma Make Prompt

Generate Figma Make-ready prompts based on design briefs and design systems.

## When to use

- After creating a design brief
- When you need automated design generation in Figma
- Before prototyping

## Critical constraint

Figma Make prompts are limited to **maximum 5000 characters**.

## Structural constraints

- Maximum 2 pages
- Maximum 6 components per page
- Maximum 8 copy items per page
- Layout description: 200 characters max
- Theme description: 50 characters max

## Output

- **Location:** `initiatives/[initiative-name]/design/`
- **File:** `figma-make-prompt-[feature-name].json`

## Process

1. Collect objectives and components from design brief
2. Reference design system tokens and components
3. Apply structural constraints (2 pages, 6 components/page, 8 copy/page)
4. Map design tokens to Figma Make format
5. Optimize components (core variants, essential props only)
6. Validate against 5000-character limit

For the full JSON schema, generation rules, and optimization guidelines, see `references/generate-figma-make-prompt.md`.

Follow the writing standards in `_shared/writing-standards.md` for all outputs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mjrtl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
