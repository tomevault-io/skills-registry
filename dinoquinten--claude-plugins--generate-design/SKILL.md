---
name: generate-design
description: Generates a complete, production-ready UI from a natural-language prompt — React + Tailwind, grounded in all 8 UIX reference books + WCAG 2.2, ROIN baseline always on. Asks 4 targeted questions then writes a self-contained .tsx file and compiles it to a browser-openable preview.html. Auto-activates on "design a [X] from scratch", "build me a UI for [X]", "create a full [X] page", "generate a UI for [X]", or /uix:generate-design.
metadata:
  author: DinoQuinten
---

# generate-design

Generate complete, production-ready UIs from a prompt. Auto-activates on generation requests or via `/uix:generate-design`.

## Triggers

"design a [X] from scratch" · "build me a UI for [X]" · "create a full [X] page" · "generate a UI for [X]" · "make a [dashboard / landing page / auth screen / settings page / etc.]" · "I need a frontend for [X]" · "build this interface".

## Prerequisites

Before running the preview pipeline, the user's project must have the following installed. If any are missing, Claude should run the install command and inform the user before proceeding to generation.

```bash
npm install react react-dom
npm install -D esbuild tsx
```

- `react` + `react-dom` — bundled into the preview by esbuild
- `esbuild` — the bundler used by `render-preview.ts`
- `tsx` — the TypeScript runner used to execute `render-preview.ts` (auto-fetched via `npx` on first use)

If the project has no `package.json`, run `npm init -y` first.

## Process

### 1. Ask 4 questions in one grouped message

Send exactly this (adapting the preamble to match the user's prompt if one was given):

> I'll generate a complete React + Tailwind UI for you. Before I do, four quick questions:
>
> **1. What type of UI?**
> dashboard / landing page / auth flow (login + signup) / settings page / e-commerce / data table / other
>
> **2. What's the app about?** (1–2 sentences — e.g. "a project management tool for remote teams")
>
> **3. What are the key sections or features to show?**
> e.g. sidebar nav, stat cards, data table, charts, hero section, pricing cards, form wizard
>
> **4. Anything specific to include or avoid?** (optional — specific colors, brand name, sections to skip)

If the user's prompt already answers one or more questions, skip those and ask only the remaining ones. If the prompt is fully specific (all 4 answered), skip directly to generation.

### 2. Flag uncertainty before generating

Before writing any code, if anything in the answers is ambiguous:

> ⚠️ Not sure how to interpret "[X]" — I'll go with [Y] for now. Flag it if that's wrong.

Never silently guess. Surface the uncertainty first.

### 3. Generate the .tsx component

Name the file `[AppName].generated.tsx` (derive from the app description, kebab-case, e.g. `project-dashboard.generated.tsx`).

Write a **single self-contained `.tsx` file** following all rules in the Generation Rules section below. No external imports beyond `react` and `react-dom`. All state via `useState`. Maximum ~300 lines.

### 4. Compile and open the preview

After writing the file, run:

```bash
npx tsx "${CLAUDE_PLUGIN_ROOT}/scripts/render-preview.ts" [path/to/Component.generated.tsx]
```

Then open the generated HTML:

```bash
# Windows
start "[path/to/Component.generated.preview.html]"
# macOS
open "[path/to/Component.generated.preview.html]"
# Linux
xdg-open "[path/to/Component.generated.preview.html]"
```

### 5. Invite iteration

After the preview opens:

> Preview is open. Tell me what to change — layout, color, sections, content, interactions — and I'll regenerate.

Apply feedback by rewriting the `.tsx`, rerunning the helper, and reopening the browser. No new questions asked unless the fundamental UI type changes.

---

## Generation Rules

Every generated component MUST apply all rules below. These are non-negotiable.

### ROIN Baseline (Return on Interaction) `[DI][UXB]`

- **Instant Gratification** — the primary action is immediately visible and clickable within 2 seconds of opening
- **Curiosity** — at least one element that makes the user think "what does that do?" — a glowing badge, a live counter, a sparkline, an animated status dot
- **Status motivation** — progress bars, achievement counts, or level indicators where contextually appropriate
- **Safe Exploration** — any destructive action requires a confirmation; cancel/undo always reachable
- **Satisficing** — the first obvious option in every list or nav is the correct one
- All buttons: visible depth (`shadow-lg`), hover transform (`hover:scale-[1.02]` or `hover:-translate-y-0.5`), active press (`active:scale-95`)
- All interactive elements: hover state always coded, never left at default

### Visual Hierarchy `[RUI]`

- Weight + color carry importance — never font-size alone
- Primary action: full background color + shadow. Secondary: ghost/outline. Destructive: confirmation modal
- Labels: `text-slate-400 text-sm`. Values: `text-white font-semibold` or `text-slate-900 font-semibold`
- Emphasize by de-emphasizing competition: mute secondary elements rather than inflating primary

### Layout & Spacing `[RUI][DI]`

- Tailwind spacing scale only: `p-1 p-2 p-3 p-4 p-6 p-8 p-10 p-12 p-16`. No arbitrary values (`p-[17px]` is forbidden)
- Generous whitespace — never cramped
- Pick the Tidwell pattern that matches the UI type:
  - Dashboard → `Center Stage + Module Tabs` (main content dominates, tabs for views)
  - Landing page → `Feature-Search-Browse` (hero + feature grid + social proof)
  - Auth flow → `Wizard + Progress Indicator` (step-by-step, never show all fields at once)
  - Settings → `Settings Editor + Accordion` (grouped, collapsible sections)
  - Data-heavy → `Two-Panel Selector + Cards` (list left, detail right)

### User Behavior `[DMMT][DI][UXB]`

- Every page passes the 5-second test: what is this, what can I do, why here, where do I start `[DMMT]`
- Persistent nav: logo as home link, main section links, utility icons (search, profile, notifications) `[DMMT]`
- Structure content for scanning, not reading: short labels, bold values, visual groupings `[DMMT]`
- Maximum 7 items in any list or nav before grouping (Miller's Law) `[UXB]`
- Large, closely spaced primary CTAs (Fitts's Law) `[UXB]`
- Immediate visible reward on first interaction (Hyperbolic discounting) `[UXB]`

### Color & Light `[RUI][IOC][C&L]`

- HSL-based palette: warm-tinted greys (`slate-*`), 1–2 vivid primaries, semantic status colors
- Never pure `#000` or `#fff` — use `slate-950` for darkest, `white/95` or `slate-50` for lightest `[C&L]`
- Shadows always tinted, never black: `shadow-indigo-900/30`, `shadow-violet-900/20`, etc. `[C&L]`
- No two adjacent elements of equal luminance (boundary dissolves) `[IOC]`
- Color never carries meaning alone — always pair with icon, label, or shape `[WCAG 1.4.1]`
- Gradients include a subtle hue shift, not just lightness change `[C&L]`

### Typography `[RUI]`

- Font: `font-sans` (Inter via Tailwind, system-ui fallback)
- Scale from Tailwind: `text-xs text-sm text-base text-lg text-xl text-2xl text-3xl text-4xl` — modular, never arbitrary
- Reading blocks: `max-w-prose` to enforce 45–75 char line length
- Large headlines: `tracking-tight`. All-caps labels: `tracking-widest text-xs`
- Never fake headings with `<div>` — always semantic `<h1>`…`<h6>`

### Depth & Surfaces `[RUI][C&L]`

- Cards: `rounded-2xl bg-slate-900 shadow-xl shadow-indigo-900/20 border border-slate-800/50`
- Elevated panels: small tight shadow + larger diffuse shadow (dual-shadow technique)
- Replace borders with background-color contrast wherever possible
- Frosted glass where contextually fitting: `backdrop-blur-xl bg-white/5 border border-white/10`

### Placeholder Data

- Always realistic: real names ("Sarah Chen", "Marcus Webb"), real numbers ("$12,847", "94.3%"), real copy ("Quarterly Revenue", "Active Users")
- Never: "Lorem ipsum", "Item 1", "User Name", "0", or empty strings
- Empty states (when showing an empty list/table): centered icon + descriptive message + primary CTA

### Iteration & Copy `[MOM][CBD]`

- Every text label, CTA, and headline describes a concrete user problem or outcome — never internal jargon or feature names `[MOM]`
- Copy is framed around what the user gains ("Track revenue in real time") not what the feature does ("Revenue tracking module") `[MOM]`
- Design for revision: every generated layout is intentionally modular so sections can be swapped without rewriting the whole component `[CBD]`
- Prototype mindset: the first output is a learning artefact, not a final product — the iteration loop in step 5 is the real workflow `[CBD]`

### Accessibility `[WCAG 2.2]`

- Text contrast ≥ 4.5:1. Non-text contrast ≥ 3:1
- All interactive targets: `min-h-11 min-w-11` (44×44 px preferred; 24×24 minimum)
- Focus rings on every interactive element: `focus-visible:ring-2 focus-visible:ring-indigo-400 focus-visible:ring-offset-2 focus-visible:ring-offset-slate-950`
- Never `outline-none` without a replacement focus style
- First element in `<main>`: a visually-hidden skip link `<a href="#main-content" className="sr-only focus:not-sr-only">Skip to content</a>`
- Semantic HTML only: `<nav>` `<main>` `<header>` `<footer>` `<section>` `<button>` `<label>` `<h1>`…`<h6>`
- `<html lang="en">` always set (injected by render-preview.ts into the HTML wrapper)
- Status messages / toasts: `role="status"` or `aria-live="polite"`
- Drag-only interactions: always provide a click/button alternative `[WCAG 2.5.7]`

### Code Constraints

- Single `.tsx` file — all state, layout, and sections in one file. Sub-components defined in the same file above the default export.
- Max ~300 lines. If naturally longer, extract 2–3 sub-components as named functions within the same file.
- Tailwind only — no `style={}` props, no CSS modules, no styled-components, no arbitrary values
- No external component library (no shadcn, MUI, Chakra) unless the user explicitly requests one
- All interactive state via `useState` — no external state management
- TypeScript — use `interface` for prop types, `const` for all declarations

### Honesty Flag Rule

If any generation rule conflicts with what the user asked for, surface it:

> ⚠️ [What user asked] may conflict with [rule] because [reason]. I'll [decision]. Flag if you want to override.

Examples:
- "Removing all borders may break WCAG 2.4.7 focus visibility — I'll keep a 2px focus ring."
- "A 12-item top nav violates Miller's Law — I'll group into 5 + a More dropdown."
- "Pure black (#000) increases eye strain — I'll use slate-950 instead."

---

## Reference

Load `${CLAUDE_PLUGIN_ROOT}/references/UI_MASTER_GUIDE.md` for the full rule detail behind each source tag.

---
> Source: [DinoQuinten/claude-plugins](https://github.com/DinoQuinten/claude-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-02 -->
