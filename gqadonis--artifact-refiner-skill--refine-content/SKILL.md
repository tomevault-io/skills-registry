---
name: refine-content
description: > Use when this capability is needed.
metadata:
  author: gqadonis
---

# Refine Content

Invoke the PMPO artifact refinement loop for **content** artifacts.

## Setup

1. Set `artifact_type: content`
2. Load domain adapter from `references/domain/content.md`
3. Load template from `assets/templates/content-report.template.html`
4. Start the PMPO loop via `prompts/meta-controller.md`

## User Input

The user will provide: $ARGUMENTS

Parse the arguments for:
- Content type (blog post, documentation, README, guide)
- Tone (technical, conversational, formal, casual)
- Target audience
- Specific areas to improve (structure, clarity, SEO, tone)
- Input file path or inline content

## Default Constraints

- Normalize heading hierarchy (single H1)
- Fix broken links and references
- Improve readability score
- Optimize for SEO (meta descriptions, heading structure)
- Maintain consistent tone throughout

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gqadonis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
