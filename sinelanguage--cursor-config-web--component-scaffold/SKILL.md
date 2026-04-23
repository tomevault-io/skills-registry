---
name: component-scaffold
description: Scaffold a new React component using this repo's conventions, including tests and stories. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Component Scaffold

Create a new component following the conventions in `.context/conventions.md` and `.context/design-system.md`.

## When to Use

- Use this skill when you need a new reusable UI component.
- Use this skill when you want tests and Storybook stories created together.

## Inputs

- Component name (PascalCase, e.g. `UserCard`)
- Target path (default: `src/components/<ComponentName>/`)
- Props and variants (if any)
- Styling approach (Tailwind or tokens)

## Instructions

1. Create the component folder and files:
   - `ComponentName.tsx`
   - `ComponentName.test.tsx`
   - `ComponentName.stories.tsx`
   - `index.ts`
2. Implement the component with strict TypeScript (no `any`).
3. Use `forwardRef` only if it is truly needed.
4. Export types and component from `index.ts`.
5. Add at least one interaction test using Testing Library.
6. Add a Storybook story that documents variants and accessibility.
7. Follow naming, import order, and file organization conventions.

## Output

- A new component folder with component, test, story, and index files.
- Any required updates to barrel exports.

## Validation

- Ensure TypeScript types are explicit and correct.
- Ensure tests and stories compile without errors.
- If commands are available, run: `npm run lint` and `npm run test`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
