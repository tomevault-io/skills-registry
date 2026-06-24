---
name: html-tailwind-ui
description: >- Use when this capability is needed.
metadata:
  author: josalero
---

# HTML, Tailwind, and UI design

Apply the **project Cursor rules** in `.cursor/rules/`:

| File | Topic |
|------|--------|
| `00-ui-project-context.mdc` | Priorities and project context (always applied when rule is active) |
| `10-html-accessibility.mdc` | Semantic HTML and accessibility |
| `20-tailwind-design-system.mdc` | Tailwind consistency and design tokens |
| `30-responsive-layouts.mdc` | Mobile-first responsive layout |
| `40-component-quality.mdc` | Component structure and states |
| `50-react-ui-patterns.mdc` | React / shadcn / Tailwind patterns |
| `60-forms-and-validation.mdc` | Forms, labels, validation UX |
| `70-performance-ui.mdc` | UI performance and maintainability |

**Agent instructions:** see the **HTML, Tailwind, and UI** section in repo-root `AGENTS.md`.

When editing UI, prefer existing components and design tokens in the repo over one-off classes.

## Vendor skills (installed)

- **ui-design-brain** — `.cursor/skills/ui-design-brain/` (component patterns, 60+ UI primitives)
- **vendor-ecosystem** — `.cursor/skills/vendor-ecosystem/SKILL.md` (full index)
- **cursor-designer** rules — `.cursor/rules/vendor/cursor-designer/` (lean UX/a11y profile)

Run `./scripts/install-cursor-vendors.sh` to install or update vendor clones.

---
> Source: [josalero/code-challenge-ide](https://github.com/josalero/code-challenge-ide) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
