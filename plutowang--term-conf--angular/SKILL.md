---
name: angular
description: Auto-apply when working with Angular. Trigger this skill when the user asks to create, modify, or debug Angular components, services, directives, pipes, HTML templates, or run Angular CLI commands. Use when this capability is needed.
metadata:
  author: plutowang
---

# Angular & Nx Stack Expert

You are an expert in **Modern Angular (v19+)**. You strictly adhere to the latest syntax features and reactive patterns.

## Template Standards

1. **Control Flow**: Use `@if`, `@for`, `@switch`, `@case`. No `*ngIf`, `*ngFor`, `ngSwitch`.
2. **Variables**: Use `@let` for template vars. Avoid `*ngIf="obs$ | async as val"` aliases.
3. **Reactivity**: Use `*ngrxLet` for Observables. Import `LetDirective` in components.

## Styling

- Tailwind CSS only. No component `.scss` or `.css` files.
- Use `styles: []` and utility classes in templates.
- Use `skill tailwind-v4` to detect custom color schemas.

## Project Layout

- Use `skill nx-monorepo` if `nx.json` exists.
- Standard layout uses `src/app/`.

## Tooling

- Package Manager: `pnpm`
- Generator: `pnpm nx g ...` (Nx) or `pnpm ng g ...` (standard)
- Run: `pnpm nx serve <app>` (Nx) or `pnpm start` (standard)
- Format: `pnpm nx format:write` (Nx) or `pnpm format:write` (standard)

## Component Architecture

- **Standalone**: All components must be `standalone: true`.
- **Signals**: Prefer `input()`, `output()`, and `viewChild()` over decorators.

**Docs**: Context7 `/websites/angular_dev` · Fallback: <https://angular.dev>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plutowang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
