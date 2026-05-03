---
name: fluent-ui-guru
description: Use when creating, auditing, or refactoring UI that must strictly follow Fluent 2 design language, especially web React interfaces and Power Platform HTML web resources, PCF controls, or Vite code apps. Applies when Codex must align layout, typography, color, motion, tokens, content, accessibility, and component usage to Fluent 2 while selecting icons from Flicon.
metadata:
  author: satriotsubasa
---

# Fluent UI Guru

## Overview

Follow Fluent 2 as the mandatory design language. Use Fluent UI React v9 Storybook as the React implementation reference when the stack supports it. Use Flicon as the mandatory icon discovery source.

## Core Rules

- Treat Fluent 2 as the source of truth for visual language, design principles, layout, color, typography, motion, content, accessibility, and tokens.
- Treat Fluent UI React v9 Storybook as the implementation reference for React projects. Do not let it override Fluent 2 foundations.
- Treat Flicon as the mandatory icon discovery and selection source. Do not silently switch to another icon library.
- Read the relevant Fluent 2 pages before proposing or implementing UI changes.
- Read the relevant component pages under the Fluent 2 React component tree for every component in scope.
- If the task is an audit or refactor, enumerate the deviations before changing code.
- If exact Fluent fidelity is blocked by platform, framework, or host constraints, stop and ask for approval before making a compromise.

## Workflow

### 1. Classify the task

Decide whether the request is:
- New UI creation
- Audit or review
- Refactor toward Fluent conformance
- Concrete implementation in an existing stack

### 2. Load the right references

- Always read [source-of-truth.md](references/source-of-truth.md) first.
- Read [icon-policy.md](references/icon-policy.md) whenever the UI includes icons.
- Read [audit-refactor-checklist.md](references/audit-refactor-checklist.md) for audits, reviews, or conformance refactors.
- Read [power-platform-adaptations.md](references/power-platform-adaptations.md) for Power Platform HTML web resources, PCF, or Vite-based code apps.

### 3. Apply source precedence

Use this order:
1. Fluent 2 foundations and UX guidance
2. Fluent 2 React component docs
3. Fluent UI React v9 Storybook for React implementation details
4. Flicon for icon discovery and selection only

Do not invert this order.

### 4. Produce or review the design

- Match Fluent 2 patterns before inventing custom ones.
- Use Fluent tokens, spacing logic, typography hierarchy, and accessibility guidance where applicable.
- Keep icon choices semantically literal and consistent with Fluent iconography guidance, but source the icon names from Flicon.

### 5. Escalate when blocked

Stop and ask for approval when:
- The host platform cannot support an exact Fluent pattern
- The only available icon is outside the Flicon corpus
- Existing product constraints force a non-Fluent visual compromise
- A requested change conflicts with Fluent 2 guidance

## Audit Output

For audits and refactors, report findings before edits:
- List each deviation from Fluent 2 or the Flicon icon policy
- State which source page or rule it conflicts with
- Recommend the Fluent-aligned correction
- Call out any issue that requires approval because exact compliance is blocked

## Power Platform Note

Power Platform is a common target, not the only target. For HTML web resources, PCF, and Vite-based code apps, adapt the implementation details to the host platform without relaxing the default Fluent 2 requirement. Read [power-platform-adaptations.md](references/power-platform-adaptations.md) before proposing code or markup changes in those environments.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/satriotsubasa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
