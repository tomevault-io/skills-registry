---
name: vue-frontend-review
description: Unified review skill for Vue front-end projects. Combines Vue2/Vue3 compatibility checks and Web Interface Guidelines UI compliance checks. Use when users ask for Vue compatibility review, migration-safe规范, browser support checks, UI/UX audit, accessibility checks, or front-end best-practice review for .vue/.ts/.js/.css/build config files. Use when this capability is needed.
metadata:
  author: miya511250
---

# Vue Frontend Review

## Overview
Use this single skill to run two review tracks together for Vue projects:
1. Vue compatibility track (Vue 2/3 API, migration safety, browser/runtime compatibility)
2. UI guidelines track (Web Interface Guidelines compliance for UI/UX/accessibility quality)

Load both references before producing findings:
- [Vue compatibility checklist](./references/compat-checklist.md)
- [UI guidelines workflow](./references/ui-guidelines.md)


## When to Use
Trigger this skill when the user asks for:
- Vue2/Vue3 compatibility review
- Vue migration risk checks
- Browser support / polyfill / transpilation compatibility checks
- UI review, accessibility audit, design/UX best-practice checks
- One-pass front-end规范审查 for Vue repos

## Inputs to Collect
Identify or infer:
- Vue major version: `2.x`, `3.x`, or mixed
- Build toolchain: `Vite`, `Webpack`, `Vue CLI`, `Nuxt`
- Target browser baseline (if missing, assume modern evergreen + iOS Safari last 2 major versions)
- Review scope: `src/**/*.vue`, `src/**/*.ts|js`, styles, plus build/config files

If key info is missing, proceed with explicit assumptions.

## Workflow
1. Run Vue compatibility track using [compat-checklist](./references/compat-checklist.md).
2. Run UI guidelines track using [ui-guidelines](./references/ui-guidelines.md).
3. Merge findings and de-duplicate overlapping items.
4. Prioritize by severity:
- `P0` release blocker or runtime break
- `P1` high risk regression/compatibility issue
- `P2` standards drift or preventive fix

## Output Format
For each finding:

`[Severity] path:line - Issue`
`Track: Vue-Compat | UI-Guidelines`
`Impact: ...`
`Recommendation: ...`

Then provide:
- `Compatibility baseline used`
- `UI guidelines source used`
- `Open assumptions`
- `Quick fix plan` (3-6 steps)

## Practical Rules
- Prefer explicit, testable rules over style opinions.
- Do not claim browser compatibility without checking build targets and polyfills.
- For mixed Vue 2/3 migration, prefer migration-safe patterns.
- Always fetch latest Web Interface Guidelines before UI findings.
- If no issues are found, state that explicitly and include residual risks.

---
> Source: [miya511250/vue-frontend-review](https://github.com/miya511250/vue-frontend-review) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-04 -->
