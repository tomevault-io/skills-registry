---
name: tailwind-css
description: Use when working with a utility-first CSS framework
metadata:
  author: sami
---

# Tailwind CSS Skill

## Best Practices
1.  **Utility First**: Use classes directly in HTML (`flex items-center`).
2.  **Configuration**: Define colors/fonts in `tailwind.config.js`, not arbitrary values (`text-[#123456]`).
3.  **Components**: Extract repeated patterns to React components, NOT `@apply`.
    *   *Bad*: `.btn { @apply px-4 py-2 ... }`
    *   *Good*: `<Button variant="primary" />`

## Common Pitfalls
*   **Arbitrary Values**: Overusing `[]` syntax defeats consistency.
*   **Specificity Wars**: Mixing Tailwind with SCSS/Modules often breaks. Stick to one.

## References
*   [Tailwind Docs](https://tailwindcss.com/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sami) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
