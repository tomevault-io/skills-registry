---
name: ui-prompt-generation
description: Use when generating detailed, technical UI prompts for designers or AI tools (v0, Bolt) that require precise specifications, tokens, and comprehensive state definitions.
metadata:
  author: valec3
---

# UI Prompt Generation

## Overview

**UI Prompt Generation IS the translation of user intent into precise, token-based technical specifications.**

You act as a bridge between vague requirements and pixel-perfect implementation. You do not "guess" designs; you assemble them from standardized, proven assets.

**Core Principle:** If a prompt doesn't reference specific design tokens and patterns, it is a failure.

## When to Use

**Use this skill when:**
**Use this skill when:**
- Generating **Production-Ready** technical prompts for AI builders
- Creating **Exhaustive** component specifications (States, Tokens, A11y)
- Translating functional requirements into **precise Tailwind/CSS tokens**
- You need a "Huge", "Detailed", or "Complete" output
- Creating design briefs that require strict accessibility & responsive rules

**Don't use when:**
- Brainstorming high-level concepts (use `ui-design-concepts`)
- Writing actual code implementation (use coding skills)
- The user asks for a simple text description without technical detail

## Core Pattern

**Before (Vague):**
> "Make a login page. It should look clean."

**After (Standardized):**
> **[Agent quietly generates `results/login-prompt.txt`]**
> "Login Page Specification:
> - **Layout**: Centered Card (Pattern: `ui-patterns.md` Type 1)
> - **Tokens**: Spacing `spacing-xl`, Shadow `shadow-lg`, Radius `radius-md`
> - **States**: Input default: `neutral-200` border; Focus: `primary-500` ring.
> - **Accessibility**: High contrast text (`neutral-900`), explicit labels."

## Implementation

**CRITICAL RULE: OUTPUT MUST BE A PLAIN TEXT `.txt` FILE IN `results/`.**
**NEVER** use Markdown syntax (no `#`, no `**`, no `[ ]`, no code blocks) inside the `.txt` file.
**ALWAYS** use clean Plain Text formatting:
- Use UPPERCASE for section headers.
- Use indentation for hierarchy.
- Use simple text labels.

**CRITICAL RULE: BE EXHAUSTIVE & VERBOSE.**
- The user expects a "huge", production-ready spec. **DO NOT SUMMARIZE.**
- **Components**: List every single state (default, hover, active, focus, disabled, loading, error).
- **Tokens**: Explicitly state every Tailwind class/token (color, spacing, shadow, radius, font-size).
- **Logic**: Detail edge cases, empty states, and validation rules.
- **Responsiveness**: Define specific behaviors for Mobile (<640px), Tablet (768px), and Desktop (1024px+).
- **Volume**: If the output is short, it is WRONG. Aim for maximum density.

### Phase 1: Context & Asset Loading
1.  **Analyze Request**: Identify the component (e.g., "Dashboard", "Form").
2.  **Load Assets (MANDATORY)**: You **MUST** use `view_file` on these files before generating:
    -   `assets/design-tokens.md` (Get exact color/spacing values)
    -   `assets/ui-patterns.md` (Select the structural pattern)
    -   `assets/component-checklist.md` (Ensure all states are covered)
    -   `assets/prompt-templates.md` (Use a base structure if applicable)

### Phase 2: Construction
Build the prompt using the data from the assets. Your prompt must include:
-   **Purpose**: User goal.
-   **Structure**: distinct sections for Layout, Visuals, States, Interactions.
-   **Tokens**: referenced exactly (e.g., `primary-500`, not "blue").
-   **Accessibility**: ARIA roles, focus states, contrast checks.

### Phase 3: Delivery
1.  **Write File**: Save the complete content to `results/<descriptive-name>.txt`.
2.  **Notify**: Tell the user "Prompt generated: `results/<filename>.txt`".

## Common Mistakes

### ❌ Ignoring Tokens
**Bad**: "Use a light gray background."
**Good**: "Background: `neutral-50`."
**Fix**: Always have `design-tokens.md` open.

### ❌ Missing States
**Bad**: "A button that submits."
**Good**: "Primary Button: Default `bg-primary-600 text-white`, Hover `bg-primary-700`, Focus `ring-2 ring-offset-2 ring-primary-500`, Disabled `bg-neutral-300 text-neutral-500 cursor-not-allowed`."
**Fix**: If you aren't listing at least 3 states, you are failing. Consult `component-checklist.md`.

### ❌ Chat Dump
**Bad**: Printing the prompt in the extensive markdown block in chat.
**Good**: "Saved to `results/prompt.txt`."
**Fix**: Check your output tool. If it's not `write_to_file`, stop.

## Quick Reference

| Asset | Purpose |
| :--- | :--- |
| **`design-tokens.md`** | **REQUIRED**. Colors, spacing, typography, shadows. |
| **`ui-patterns.md`** | **REQUIRED**. Layouts, cards, navigation structures. |
| **`component-checklist.md`** | **REQUIRED**. States, accessibility, edge cases. |
| **`prompt-templates.md`** | Starter skeletons for common pages. |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
