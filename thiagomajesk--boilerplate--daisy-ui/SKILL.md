---
name: daisyui-5
description: Build, refactor, and review UI using daisyUI 5 with Tailwind CSS 4. Use when requests involve daisyUI components, class selection, theme setup/customization, migration to daisyUI 5, or enforcing valid daisyUI and Tailwind-only styling patterns in HTML/HEEx/JSX templates. Use when this capability is needed.
metadata:
  author: thiagomajesk
---

# daisyUI 5

Implement UI with valid daisyUI 5 and Tailwind CSS 4 patterns. Prefer daisyUI component classes first, then Tailwind utilities for layout/spacing/overrides.

## Workflow

1. Confirm stack assumptions:
- Use Tailwind CSS 4 syntax.
- Avoid `tailwind.config.js` guidance unless the project explicitly requires legacy behavior.

2. Set up styles correctly:
- Ensure CSS includes `@import "tailwindcss";`.
- Ensure CSS includes `@plugin "daisyui";`.
- Add `@plugin "daisyui/theme"` only when a custom theme is requested.

3. Build UI with class discipline:
- Use only daisyUI classes and Tailwind utility classes.
- Prefer semantic daisyUI colors (`primary`, `base-100`, `error`, etc.) over fixed Tailwind colors.
- Use responsive utilities for flex/grid layouts.
- Avoid custom CSS unless class-based solutions are insufficient.

4. Apply component syntax exactly:
- Follow component class names, parts, modifiers, and rules from the reference file.
- Preserve required structure for components (for example accordion, modal, dropdown patterns).

5. Validate output before finishing:
- Check for invalid or non-existent daisyUI classes.
- Check text/background contrast with `*-content` colors.
- Check light/dark behavior through theme semantics (avoid unnecessary `dark:` for daisyUI semantic colors).

## Reference

- Core guidance and component catalog: `references/usage-guide.md`

When searching the reference quickly, use these patterns:
- `^## daisyUI 5 install notes`
- `^## daisyUI 5 usage rules`
- `^## Config`
- `^## daisyUI 5 colors`
- `^### [a-z0-9-]+$` (component sections)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thiagomajesk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
