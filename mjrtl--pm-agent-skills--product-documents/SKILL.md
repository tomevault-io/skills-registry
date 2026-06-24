---
name: product-documents
description: > Use when this capability is needed.
metadata:
  author: mjrtl
---

# Product Documents

This skill generates four types of product documents:

1. **PRD (Product Requirements Document)** - See `references/create-prd.md`
2. **1-Pager** - See `references/create-1-pager.md`
3. **Design Brief** - See `references/create-design-brief.md`
4. **Figma Make Prompt** - See `references/generate-figma-make-prompt.md`

---

## PRD (Product Requirements Document)

### Goal

Create a detailed PRD in Markdown format based on an initial user prompt. The PRD should be clear, actionable, and suitable for a junior developer to understand and implement.

### Process

1. **Receive initial prompt:** The user provides a brief description or request for a new feature.
2. **Ask clarifying questions:** Ask only the most essential 3-5 clarifying questions. Provide lettered options (A, B, C, D) for easy response.
3. **Generate PRD:** Based on the prompt and answers, generate the PRD.
4. **Save PRD:** Save as `prd-[feature-name].md` inside the `/prd/` directory.

### PRD sections

1. Introduction/Overview
2. Goals
3. User Stories
4. Functional Requirements (numbered)
5. Non-Goals (Out of Scope)
6. Design Considerations (optional)
7. Technical Considerations (optional)
8. Success Metrics
9. Open Questions

### Rules

- Do NOT start implementing the PRD
- Always ask clarifying questions first
- Take the user's answers and improve the PRD

---

## 1-Pager

### Goal

Create a **decision-focused 1-Pager** in narrative document format. Readable within 5 minutes while providing enough context for fast, high-quality decision-making.

The core purpose is to verify whether the Outcome and Opportunity are truly valuable enough to be addressed as initiatives or projects.

### Process

1. **Clarifying questions:** Collect information about Outcome, Opportunity, Lessons, Solutions, Assumptions, and Decision Request.
2. **Narrative writing:** Write each section as a storytelling paragraph.
3. **Bullet summary:** After each paragraph, organize 2-4 key points as bullets.
4. **Risk framework:** In Solutions & Assumptions, examine assumptions using four axes: Value, Viability, Feasibility, Usability Risk.
5. **Decision triggers:** End with clear decision points for leadership discussion.

### Sections

- Outcome -> Opportunity -> Lessons -> Solutions & Assumptions -> Decision Requests

### Output

- **Format:** Markdown (`.md`)
- **Location:** `/prd/`
- **Filename:** `1-pager-[initiative-name].md`

---

## Design Brief

### Goal

Generate consistent design briefs outputting both machine-readable JSON (for Figma/Make and prototyping tools) and stakeholder-friendly Markdown summaries.

### Output

- **Location:** `initiatives/[initiative-name]/design/`
- **Files:** `design-brief-[feature-name].json` and `design-brief-[feature-name].md`

### Process

1. **Brief intake**: Collect product/campaign objectives, constraints, deadlines, and stakeholders.
2. **Design system reference**: Reference tokens and components from the project's design system.
3. **Library mapping**: Prioritize existing components/patterns.
4. **Token first**: Define color, typography, spacing variables first, then inject into components.
5. **Channel consistency**: Ensure Ads/Branding/Social/Prototype share the same variables.
6. **Accessibility gate**: Check WCAG 2.2 AA, minimum 44px touch, RTL/multilingual support.
7. **Output dual-track**: Generate JSON + Markdown simultaneously.
8. **File storage**: Save to `initiatives/[initiative-name]/design/` folder.

For full JSON schema and Markdown template, see `references/create-design-brief.md`.

---

## Figma Make Prompt

### Goal

Generate Figma Make-ready prompts based on design briefs and design systems.

**Critical constraint**: Figma Make prompts are limited to **maximum 5000 characters**.

### Structural constraints

- Maximum 2 pages
- Maximum 6 components per page
- Maximum 8 copy items per page
- Layout description: 200 characters max
- Theme description: 50 characters max

### Output

- **Location:** `initiatives/[initiative-name]/design/`
- **File:** `figma-make-prompt-[feature-name].json`

For full JSON schema and generation rules, see `references/generate-figma-make-prompt.md`.

---

Follow the writing standards in `_shared/writing-standards.md` for all outputs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mjrtl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
