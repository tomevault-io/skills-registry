---
name: reactcomponents
description: Converts Stitch designs into modular React components using TypeScript, CSS custom properties, and automated validation.
metadata:
  author: harvy2702
---

# Stitch to React Components

You are a React engineer focused on transforming Stitch designs into clean, idiomatic React components. You follow a modular approach and use automated tools to ensure code quality.

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
3. **Generate shared components first** under `src/components/shared/`. Individual page files import from this directory instead of duplicating code.
4. **Parameterise shared components** — e.g. a `PrimaryButton` that accepts `label`, `onClick`, and `isLoading` — rather than creating a near-identical button component per screen.

## Architectural rules

### State management — detect, don't dictate
* Inspect `package.json` for the project's existing state management (e.g. `redux`, `@reduxjs/toolkit`, `zustand`, `jotai`, `recoil`, `mobx`).
* **Follow whatever pattern is already in use.** Do not introduce a second state management library.
* If no state management exists, use React hooks (`useState`, `useReducer`) for local UI state. Recommend a library only when the complexity clearly warrants it.
* Keep components purely presentational: pass data and callbacks in via props. A component should never know *how* state is managed — only *what* data it displays and *what* events it fires.

### Component structure
* **Functional components only**: Use arrow functions or `function` declarations with `React.FC<Props>` or explicit typed props. Never use class components.
* **TypeScript required**: All components must be `.tsx` files. Define a `Props` interface for every component. All props must be explicitly typed — no `any`.
* **Modular files**: Break the design into independent component files under `src/components/`. Avoid large, single-file outputs.
* **Component decomposition**: Extract a sub-component when a component exceeds ~80 lines or when a subtree is reused. Prefer composition over deeply nested JSX.
* **Data decoupling**: Move all static text, image URLs, and lists into `src/data/mockData.ts`.
* **Named exports**: Always use named exports (not default exports) for better refactoring support.
* **Project specific**: Focus on the target project's needs and constraints.

### Styling — detect, then apply
* Inspect `package.json` for the project's existing styling approach:
  - **CSS Modules** (`.module.css`/`.module.scss`) — the default if nothing is detected.
  - **Tailwind CSS** — if `tailwindcss` is present, use utility classes.
  - **styled-components / Emotion** — if present, use tagged template literals.
  - **Vanilla CSS** — if plain `.css` imports are used, follow the pattern.
* **Follow whatever approach is already in use.** Do not introduce a second styling library.

### Theming and design tokens
* Extract the color palette, typography, and spacing values from the HTML `<head>`.
* Sync these values with `references/style-guide.json`.
* Define CSS custom properties in a root theme file (e.g. `src/styles/theme.css`):
  ```css
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
* Reference tokens via `var(--color-primary)` in CSS — never hardcode hex values inline (no `color: #19e65e` or `style={{ color: '#19e65e' }}`).
* Support both light and dark themes via a `data-theme` attribute or CSS `prefers-color-scheme` media query.

### Responsive layout
* Use CSS Flexbox and Grid for layout. Never assume a fixed viewport width.
* Use `min()`, `max()`, `clamp()`, and container queries or media queries for responsive breakpoints.
* Avoid hardcoded pixel widths on containers; prefer `max-width`, `flex`, or percentage-based sizing.

### Accessibility
* Use semantic HTML elements (`<nav>`, `<main>`, `<article>`, `<button>`, etc.) instead of generic `<div>` and `<span>`.
* Add `aria-label` to interactive elements that lack visible text.
* Ensure all clickable elements are keyboard-accessible (`<button>`, not `<div onClick>`).
* Ensure sufficient color contrast (WCAG 2.1 AA minimum).

### Image handling
* Always provide an `alt` attribute on `<img>` elements.
* Use a fallback/placeholder for network images via `onError` handlers.
* If `next/image` is available (Next.js projects), prefer `<Image>` for optimization.

### Performance
* Wrap expensive computations in `useMemo` and callbacks in `useCallback` when they are passed as props.
* Use `React.memo` for components that receive stable props but re-render due to parent changes.
* Lazy-load below-the-fold sections with `React.lazy` and `Suspense`.

### Routing awareness
* Check for existing routing (`react-router`, `next/router`, `@tanstack/router`) before wiring new pages. Match the existing pattern. Do not introduce a second router.

## Execution steps
1. **Project audit**: Inspect `package.json` to detect state management, routing, styling libraries, and build tool (Vite, Next.js, CRA) already in use. Adapt all subsequent steps to the project's existing patterns.
2. **Environment setup**: If `node_modules` is missing, run `npm install` to resolve dependencies.
3. **Multi-screen scan** (if multiple screens): Follow the *Multi-screen analysis* section above to build a component inventory and create shared components first.
4. **Data layer**: Create `src/data/mockData.ts` based on the design content.
5. **Theme setup**: Create `src/styles/theme.css` with CSS custom properties extracted from the Stitch design, cross-referencing `references/style-guide.json`.
6. **Component drafting**: Use `assets/component-template.tsx` as a base. Find and replace all instances of `StitchComponent` with the actual name of the component you are creating.
7. **Application wiring**: Update the project entry point (`src/App.tsx` or relevant page) to render the new components with the project's existing router.
8. **Quality check**:
    * Run `npx tsc --noEmit` to catch type errors.
    * Run `node scripts/validate_component.mjs <file_path>` for each component to check for hardcoded colors and missing conventions.
    * Verify the final output against `references/architecture-checklist.md`.
    * Start the app with `npm run dev` to verify the live result.

## Troubleshooting
* **Fetch errors**: Ensure the URL is quoted in the bash command to prevent shell errors.
* **Type errors**: Review `npx tsc --noEmit` output and fix any missing types, incorrect interfaces, or lint warnings.
* **Theme issues**: If colors don't match the design, re-check `references/style-guide.json` mapping and ensure CSS custom properties are applied at the `:root` level.
* **State management conflicts**: If the project uses Redux but generated code imports Zustand, remove the wrong import and adapt to the project's state layer.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/harvy2702) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
