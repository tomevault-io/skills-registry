---
name: atom-smith
description: Generates base UI Atoms (Button, Input, Badge, Avatar) with strict TypeScript props and Tailwind-ready class patterns.
metadata:
  author: cargdev
---

# Atom Smith

When to use this skill

- When adding or standardizing small UI building blocks used across the app.
- Triggered by requests to scaffold typed atoms with accessible defaults.

Instructions

1. First Step: Create `src/components/atoms/<AtomName>.tsx` with a minimal, accessible markup and a typed props interface.

2. Second Step: Add `index.ts` to the atoms folder to export the atom and document common props in JSDoc or comments.

3. Third Step: Provide example stories (if Storybook exists) or a test that confirms basic render and accessibility attributes.

Examples

- Button atom snippet:

```tsx
export type ButtonProps = React.ButtonHTMLAttributes<HTMLButtonElement> & { variant?: 'primary' | 'ghost' }
export const Button: React.FC<ButtonProps> = ({ children, className, variant = 'primary', ...rest }) => (
  <button className={`px-3 py-1 rounded ${variant === 'primary' ? 'bg-blue-600 text-white' : 'bg-transparent' } ${className ?? ''}`} {...rest}>{children}</button>
)
```

Notes

- Ensure atoms are accessible by default (proper role, aria-labels when appropriate, keyboard focus).
- Keep atoms small; styling via Tailwind utility classes is recommended for consistency.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cargdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
