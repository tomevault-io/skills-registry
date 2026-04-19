---
name: angularcomponents
description: Converts Stitch designs into modular Angular standalone components using signals, OnPush change detection, and automated validation.
metadata:
  author: harvy2702
---

# Stitch to Angular Components

You are an Angular engineer focused on transforming Stitch designs into clean, idiomatic Angular components. You follow a modular approach targeting Angular 19+ with standalone components and use automated tools to ensure code quality.

## Retrieval and networking
1. **Namespace discovery**: Run `list_tools` to find the Stitch MCP prefix. Use this prefix (e.g., `stitch:`) for all subsequent calls.
2. **Metadata fetch**: Call `[prefix]:get_screen` to retrieve the design JSON.
3. **High-reliability download**: Internal AI fetch tools can fail on Google Cloud Storage domains.
   - Use the `Bash` tool to run: `bash scripts/fetch-stitch.sh "[htmlCode.downloadUrl]" "temp/source.html"`.
   - This script handles the necessary redirects and security handshakes.
4. **Visual audit**: Check `screenshot.downloadUrl` to confirm the design intent and layout details.

## Multi-screen analysis
When converting multiple screens at once, identify reusable elements **before** generating any components:

1. **Download all screen HTMLs first.** Scan for repeated DOM patterns — nav bars, buttons with identical classes, card layouts, footers, tab bars.
2. **Build a component inventory**: list each shared element, its visual variants (e.g. filled vs. outlined button), and which screens use it.
3. **Generate shared components first** under `src/app/shared/`. Individual page components import from this directory instead of duplicating code.
4. **Parameterise shared components** — e.g. a `PrimaryButtonComponent` that accepts `label`, `click`, and `isLoading` inputs — rather than creating a near-identical button component per page.

## Architectural rules

### State management — detect, don't dictate
* Inspect `package.json` for the project's existing state management (e.g. `@ngrx/store`, `@ngxs/store`, `@ngrx/signals`, Akita).
* **Follow whatever pattern is already in use.** Do not introduce a second state management library.
* If no state management exists, use Angular signals (`signal()`, `computed()`, `effect()`) for local reactive state. Recommend NgRx/NGXS only when the complexity clearly warrants it.
* Keep components purely presentational: pass data via `input()` signals and emit events via `output()`. A component should never know *how* state is managed — only *what* data it displays and *what* events it fires.

### Component structure
* **Standalone components only**: Every component must use `standalone: true`. Never create NgModule-based components.
* **Signal-based I/O**: Use the new `input()`, `output()`, and `model()` signal functions instead of `@Input()` and `@Output()` decorators.
* **OnPush change detection**: Every component must use `changeDetection: ChangeDetectionStrategy.OnPush`.
* **Modular files**: Break the design into independent component files under `src/app/components/`. Avoid large, single-file outputs.
* **Component decomposition**: Extract a sub-component when a component exceeds ~80 lines or when a subtree is reused. Prefer composition over deeply nested templates.
* **Data decoupling**: Move all static text, image URLs, and lists into `src/app/data/mock-data.ts`.
* **Project specific**: Focus on the target project's needs and constraints.

### Styling — detect, then apply
* Inspect `angular.json` and existing components for the project's styling strategy:
  - **Component SCSS** (`.component.scss`) — the default if nothing specific is detected.
  - **Tailwind CSS** — if `tailwindcss` is in `devDependencies`, use utility classes.
  - **Component CSS** (`.component.css`) — if plain CSS is used, follow the pattern.
* **Follow whatever approach is already in use.** Do not introduce a second styling approach.
* Use `ViewEncapsulation.Emulated` (the default) for scoped styles.

### Theming and design tokens
* Extract the color palette, typography, and spacing values from the HTML `<head>`.
* Sync these values with `references/style-guide.json`.
* Define CSS custom properties in a global theme file (e.g. `src/styles.scss` or `src/theme.css`):
  ```scss
  :root {
    --color-primary: #19e65e;
    --color-background: #f6f8f6;
    --color-surface: #ffffff;
    --text-body-font: 'Inter', sans-serif;
    --spacing-sm: 8px;
    --radius-md: 12px;
  }
  [data-theme="dark"] {
    --color-background: #112116;
    --color-surface: #1a2e1f;
  }
  ```
* Reference tokens via `var(--color-primary)` in component styles — never hardcode hex values inline.
* Support both light and dark themes via a `data-theme` attribute or CSS `prefers-color-scheme` media query.

### Responsive layout
* Use CSS Flexbox and Grid for layout. Never assume a fixed viewport width.
* Use `min()`, `max()`, `clamp()`, and `@media` queries for responsive breakpoints.
* Avoid hardcoded pixel widths on containers; prefer `max-width`, `flex`, or percentage-based sizing.

### Accessibility
* Use semantic HTML elements (`<nav>`, `<main>`, `<article>`, `<button>`, etc.) instead of generic `<div>` and `<span>`.
* Add `aria-label` to interactive elements that lack visible text.
* Ensure all clickable elements are keyboard-accessible (`<button>`, not `<div (click)>`).
* Ensure sufficient color contrast (WCAG 2.1 AA minimum).

### Image handling
* Always provide an `alt` attribute on `<img>` elements.
* Use a fallback/placeholder for network images via `(error)` event handlers.
* If `NgOptimizedImage` is available, prefer it for automatic optimization.

### Performance
* Use `OnPush` change detection on every component to minimize change detection cycles.
* Use `@defer` blocks for below-the-fold content to enable lazy loading.
* Avoid `ngOnChanges` — prefer `computed()` signals for derived state.
* Use `trackBy` (or `track` in `@for`) for list rendering to minimize DOM thrashing.

### Routing awareness
* Check for existing routing (`@angular/router` with standalone `provideRouter`) before wiring new pages. Match the existing pattern. Do not introduce a second router.
* Use lazy loading with `loadComponent` for route-level code splitting.

## Execution steps
1. **Project audit**: Inspect `package.json` and `angular.json` to detect Angular version, state management, routing, and styling approach. Adapt all subsequent steps to the project's existing patterns.
2. **Environment setup**: If `node_modules` is missing, run `npm install` to resolve dependencies.
3. **Multi-screen scan** (if multiple screens): Follow the *Multi-screen analysis* section above to build a component inventory and create shared components first.
4. **Data layer**: Create `src/app/data/mock-data.ts` based on the design content.
5. **Theme setup**: Add CSS custom properties to `src/styles.scss` extracted from the Stitch design, cross-referencing `references/style-guide.json`.
6. **Component drafting**: Use `assets/component-template.ts` as a base. Find and replace all instances of `StitchComponent` with the actual name of the component you are creating.
7. **Application wiring**: Update the project routing (`app.routes.ts`) or the relevant page to render the new components.
8. **Quality check**:
    * Run `npx ng build` or `npx tsc --noEmit` to catch type errors.
    * Run `node scripts/validate_component.mjs <file_path>` for each component to check for hardcoded colors and missing conventions.
    * Verify the final output against `references/architecture-checklist.md`.
    * Start the app with `ng serve` to verify the live result.

## Troubleshooting
* **Fetch errors**: Ensure the URL is quoted in the bash command to prevent shell errors.
* **Type errors**: Review `ng build` output and fix any missing types, incorrect interfaces, or lint warnings.
* **Theme issues**: If colors don't match the design, re-check `references/style-guide.json` mapping and ensure CSS custom properties are applied at the `:root` level in `src/styles.scss`.
* **State management conflicts**: If the project uses NgRx but generated code imports Akita, remove the wrong import and adapt to the project's state layer.
* **Standalone errors**: If importing a non-standalone component, wrap it with `importProvidersFrom()` or migrate it to standalone.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/harvy2702) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
