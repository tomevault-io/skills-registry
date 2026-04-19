---
name: modern-ui-quality-gate
description: Enforces modern, minimal, professional UI standards and rejects outdated or visually noisy web design patterns. Use when this capability is needed.
metadata:
  author: mohamed-g-shoaib
---

# Modern UI Quality Gate

## When to use this skill

- Generating UI components
- Writing CSS or Tailwind
- Designing layouts
- Styling pages or design systems

## Priority

**Critical (must never violate):**

- Mobile-first layout
- Keyboard operable + visible focus
- Respect prefers-reduced-motion
- No pre-2020 aesthetics

**High:**

- Whitespace > decoration
- Minimal shadows/borders/gradients
- Hit targets ≥44px mobile / ≥24px desktop

## UI Enforcement Rules

- Prefer whitespace, alignment, and typography over decoration.
- Avoid gradients unless subtle and clearly justified.
- Avoid glow, neon, glassmorphism, or novelty effects.
- Avoid heavy shadows; if used, keep them soft and minimal.
- Use borders sparingly and consistently.
- Visual hierarchy must be created via spacing and font weight, not effects.

# Web Interface Guidelines

Interfaces succeed because of hundreds of choices. This is a living, non-exhaustive list of those decisions. Most guidelines are framework-agnostic, some specific to React/Next.js.

## [Interactions](#interactions)

- All flows are keyboard-operable & follow the WAI-ARIA Authoring Patterns.
- Every focusable element shows a visible focus ring. Prefer `:focus-visible`.
- Use focus traps, move & return focus according to WAI-ARIA Patterns.
- Exception: if visual target <24px, expand hit target to ≥24px. On mobile ≥44px.
- `<input>` font size ≥16px on mobile. Or set viewport meta to prevent zoom.
- Never disable browser zoom.
- Inputs must not lose focus or value after hydration.
- Never disable paste.
- Loading buttons: show indicator & keep label.
- Spinner/skeleton: add ~150–300ms delay & ~300–500ms minimum visible time.
- Persist state in URL (e.g., nuqs).
- Optimistic updates with rollback or Undo.
- Menu/loading states end with ellipsis (e.g., "Saving…").
- Confirm destructive actions or provide Undo.
- Set `touch-action: manipulation`.
- Set `webkit-tap-highlight-color`.
- Generous hit targets, clear affordances.
- Tooltip: delay first, no delay for subsequent peers.
- Set `overscroll-behavior: contain` intentionally.
- Back/Forward restores scroll.
- Autofocus primary input on desktop only.
- No dead zones on controls.
- Deep-link everything.
- Clean drag: disable selection & apply `inert`.
- Use `<a>` or `<Link>` for navigation.
- Use polite aria-live for toasts & validation.
- Internationalize keyboard shortcuts.

## [Animations](#animations)

- Honor `prefers-reduced-motion`.
- Prefer CSS > Web Animations API > JS libraries.
- Use `transform`, `opacity`; avoid reflow triggers.
- Animate only for cause/effect or delight.
- Easing fits the subject.
- Interruptible and input-driven.
- Correct transform origin.
- Never `transition: all`.
- For SVG: apply to `<g>` wrapper, set `transform-box: fill-box`.

## [Layout](#layout)

- Optical alignment (±1px when needed).
- Deliberate alignment to grid/baseline/edge.
- Balance contrast in lockups.
- Verify responsive on mobile/laptop/ultra-wide.
- Respect safe-area variables.
- No excessive scrollbars.
- Prefer flex/grid/intrinsic over JS measurement.

## [Content](#content)

- Inline help first; tooltips last.
- Stable skeletons mirror final content.
- Accurate `<title>`.
- No dead ends; all states designed.
- Typographic quotes, ellipsis character `…`.
- Avoid widows/orphans.
- Tabular numbers for comparisons.
- Redundant status cues (text + icon).
- Icons have labels.
- Don’t ship the schema.
- Anchored headings with `scroll-margin-top`.
- Resilient to user-generated content.
- Locale-aware formats.
- Prefer language settings over location.
- Accessible content (aria-label, aria-hidden).
- Icon-only buttons have aria-label.
- Semantics before ARIA.
- Hierarchical headings + skip link.
- Brand resources from logo right-click.
- Non-breaking spaces for glued terms (`&nbsp;` or `&#x2060;`).

## [Forms](#forms)

- Enter submits (single control or last).
- Textarea: ⌘/⌃+Enter submits.
- Labels everywhere; clickable.
- Submit enabled until in-flight; disable + spinner.
- Don’t block typing; validate after.
- Don’t pre-disable submit.
- No dead zones on checkboxes/radios.
- Errors next to fields; focus first on submit.
- Set `autocomplete` & meaningful `name`.
- Selective spellcheck.
- Correct `type` & `inputmode`.
- Placeholders end with ellipsis; show example.
- Warn on unsaved changes.
- Password manager & 2FA compatible.
- Don’t trigger password managers incorrectly.
- Trim input whitespace.
- Set background/color on native `<select>` for Windows.

## [Performance](#performance)

- Test iOS Low Power & Safari.
- Measure without extensions.
- Track re-renders (React DevTools).
- Throttle CPU/network when profiling.
- Minimize layout work.
- Network budgets: <500ms for mutations.
- Prefer uncontrolled inputs.
- Virtualize large lists.
- Preload above-fold images; lazy-load rest.
- No image CLS (explicit dimensions).
- Preconnect to origins.
- Preload/subset fonts.

## [Design](#design)

- Layered shadows (ambient + direct).
- Crisp borders with semi-transparent.
- Nested radii concentric.
- Hue-consistent borders/shadows.
- Accessible charts.
- Prefer APCA contrast.
- Interactions increase contrast.
- Set `<meta name="theme-color">`.
- Set `color-scheme`.
- Animate wrapper for text anti-aliasing.
- Avoid gradient banding (use masks if needed).

## [Copywriting](#copywriting)

- Active voice.
- Title Case for headings/buttons.
- Clear & concise.
- Prefer `&` over `and`.
- Action-oriented.
- Consistent nouns.
- Second person.
- Consistent placeholders.
- Numerals for counts.
- Consistent currency decimals.
- Space between numbers & units (`&nbsp;`).
- Positive language.
- Error messages guide fix.
- Avoid ambiguity.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mohamed-g-shoaib) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
