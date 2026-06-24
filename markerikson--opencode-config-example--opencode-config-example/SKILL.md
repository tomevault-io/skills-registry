---
name: web-interface-guidelines
description: Vercel's Web Interface Guidelines — a comprehensive checklist covering interactions, animations, layout, content, forms, performance, and design. Use when building or reviewing web UIs for quality, accessibility, and polish. Use when this capability is needed.
metadata:
  author: markerikson
---

# Web Interface Guidelines

> Source: [Vercel Web Interface Guidelines](https://github.com/vercel-labs/web-interface-guidelines) — a living, non-exhaustive list of interface design decisions. Most guidelines are framework-agnostic, some specific to React/Next.js.

## Interactions

- **Keyboard works everywhere.** All flows are keyboard-operable & follow the [WAI-ARIA Authoring Patterns](https://www.w3.org/WAI/ARIA/apg/patterns/).
- **Clear focus.** Every focusable element shows a visible focus ring. Prefer `:focus-visible` over `:focus` to avoid distracting pointer users. Set `:focus-within` for grouped controls.
- **Manage focus.** Use focus traps, move & return focus according to the [WAI-ARIA Patterns](https://www.w3.org/WAI/ARIA/apg/patterns/).
- **Match visual & hit targets.** Exception: if the visual target is < 24px, expand its hit target to >= 24px. On mobile, the minimum size is 44px.
- **Mobile input size.** `<input>` font size is >= 16px on mobile to prevent iOS Safari auto-zoom/pan on focus.
- **Respect zoom.** Never disable browser zoom.
- **Hydration-safe inputs.** Inputs must not lose focus or value after hydration.
- **Don't block paste.** Never disable paste in `<input>` or `<textarea>`.
- **Loading buttons.** Show a loading indicator & keep the original label.
- **Minimum loading-state duration.** If you show a spinner/skeleton, add a short show-delay (~150-300 ms) & a minimum visible time (~300-500 ms) to avoid flicker on fast responses. React `<Suspense>` does this automatically.
- **URL as state.** Persist state in the URL so share, refresh, Back/Forward navigation work (e.g., [nuqs](https://nuqs.dev)).
- **Optimistic updates.** Update the UI immediately when success is likely; reconcile on server response. On failure, show an error & roll back or provide Undo.
- **Ellipsis for further input & loading states.** Menu options that open a follow-up (e.g., "Rename...") & loading/processing states (e.g., "Loading...", "Saving...", "Generating...") end with an ellipsis.
- **Confirm destructive actions.** Require confirmation or provide Undo with a safe window.
- **Prevent double-tap zoom on controls.** Set `touch-action: manipulation`.
- **Tap highlight follows design.** Set `webkit-tap-highlight-color`.
- **Design forgiving interactions.** Controls minimize finickiness with generous hit targets, clear affordances, & predictable interactions (e.g., prediction cones).
- **Tooltip timing.** Delay the first tooltip in a group; subsequent peers have no delay.
- **Overscroll behavior.** Set `overscroll-behavior: contain` intentionally (e.g., in modals/drawers).
- **Scroll positions persist.** Back/Forward restores prior scroll.
- **Autofocus for speed.** On desktop screens with a single primary input, autofocus. Rarely autofocus on mobile because the keyboard opening can cause layout shift.
- **No dead zones.** If part of a control looks interactive, it should be interactive.
- **Deep-link everything.** Filters, tabs, pagination, expanded panels — anytime `useState` is used.
- **Clean drag interactions.** Disable text selection & apply `inert` while an element is dragged.
- **Links are links.** Use `<a>` or `<Link>` for navigation so standard browser behaviors work (Cmd/Ctrl+Click, middle-click, right-click). Never substitute with `<button>` or `<div>`.
- **Announce async updates.** Use polite aria-live for toasts & inline validation.
- **Locale-aware keyboard shortcuts.** Internationalize keyboard shortcuts for non-QWERTY layouts. Show platform-specific symbols.

## Animations

- **Honor `prefers-reduced-motion`.** Provide a reduced-motion variant.
- **Implementation preference.** CSS > Web Animations API > JavaScript libraries (e.g., motion).
- **Compositor-friendly.** Prioritize GPU-accelerated properties (`transform`, `opacity`) & avoid properties that trigger reflows/repaints (`width`, `height`, `top`, `left`).
- **Necessity check.** Only animate when it clarifies cause & effect or adds deliberate delight.
- **Easing fits the subject.** Choose easing based on what changes (size, distance, trigger).
- **Interruptible.** Animations are cancelable by user input.
- **Input-driven.** Avoid autoplay; animate in response to actions.
- **Correct transform origin.** Anchor motion to where it "physically" starts.
- **Never `transition: all`.** Explicitly list only the properties you intend to animate (typically `opacity`, `transform`).
- **Cross-browser SVG transforms.** Apply CSS transforms/animations to `<g>` wrappers & set `transform-box: fill-box; transform-origin: center;`.

## Layout

- **Optical alignment.** Adjust +/-1px when perception beats geometry.
- **Deliberate alignment.** Every element aligns with something intentionally — grid, baseline, edge, or optical center.
- **Balance contrast in lockups.** When text & icons sit side by side, adjust weight, size, spacing, or color so they don't clash.
- **Responsive coverage.** Verify on mobile, laptop, & ultra-wide. For ultra-wide, zoom out to 50% to simulate.
- **Respect safe areas.** Account for notches & insets with safe-area variables.
- **No excessive scrollbars.** Only render useful scrollbars; fix overflow issues. On macOS set "Show scroll bars" to "Always" to test what Windows users see.
- **Let the browser size things.** Prefer flex/grid/intrinsic layout over measuring in JS.

## Content

- **Inline help first.** Prefer inline explanations; use tooltips as a last resort.
- **Stable skeletons.** Skeletons mirror final content exactly to avoid layout shift.
- **Accurate page titles.** `<title>` reflects the current context.
- **No dead ends.** Every screen offers a next step or recovery path.
- **All states designed.** Empty, sparse, dense, & error states.
- **Typographic quotes.** Prefer curly quotes (" ") over straight quotes (" ").
- **Tabular numbers for comparisons.** Use `font-variant-numeric: tabular-nums` or a monospace font.
- **Redundant status cues.** Don't rely on color alone; include text labels.
- **Icons have labels.** Convey the same meaning with text for non-sighted users.
- **Don't ship the schema.** Visual layouts may omit visible labels, but accessible names/labels still exist for assistive tech.
- **Use the ellipsis character.** `...` not three periods.
- **Anchored headings.** Set `scroll-margin-top` for headers when linking to sections.
- **Resilient to user-generated content.** Layouts handle short, average, & very long content.
- **Locale-aware formats.** Format dates, times, numbers, delimiters, & currencies for the user's locale.
- **Accessible content.** Set accurate names (`aria-label`), hide decoration (`aria-hidden`) & verify in the accessibility tree.
- **Icon-only buttons are named.** Provide a descriptive `aria-label`.
- **Semantics before ARIA.** Prefer native elements (`button`, `a`, `label`, `table`) before `aria-*`.
- **Headings & skip link.** Hierarchical `<h1-h6>` & a "Skip to content" link.
- **Non-breaking spaces for glued terms.** Use `&nbsp;` to keep units, shortcuts & names together: `10 MB`, keyboard shortcuts, brand names.

## Forms

- **Enter submits.** When a text input is focused, Enter submits if it's the only control.
- **Textarea behavior.** In `<textarea>`, Cmd/Ctrl+Enter submits; Enter inserts a new line.
- **Labels everywhere.** Every control has a `<label>` or is associated with a label for assistive tech.
- **Label activation.** Clicking a `<label>` focuses the associated control.
- **Submission rule.** Keep submit enabled until submission starts; then disable during the in-flight request, show a spinner, & include an idempotency key.
- **Don't block typing.** Allow any input & show validation feedback. Blocking keystrokes is confusing.
- **Don't pre-disable submit.** Allow submitting incomplete forms to surface validation feedback.
- **No dead zones on controls.** Checkboxes & radios share a single generous hit target with their label.
- **Error placement.** Show errors next to their fields; on submit, focus the first error.
- **Autocomplete & names.** Set `autocomplete` & meaningful `name` values to enable autofill.
- **Spellcheck selectively.** Disable for emails, codes, usernames, etc.
- **Correct types & input modes.** Use the right `type` & `inputmode` for better keyboards & validation.
- **Placeholders signal emptiness.** End with an ellipsis.
- **Placeholder value.** Set placeholder to an example value or pattern.
- **Unsaved changes.** Warn before navigation when data could be lost.
- **Password managers & 2FA.** Ensure compatibility & allow pasting one-time codes.
- **Don't trigger password managers for non-auth fields.** Avoid reserved names, use `autocomplete="off"`.
- **Text replacements & expansions.** Trim input values to avoid confusing errors from trailing whitespace.
- **Windows `<select>` background.** Explicitly set `background-color` & `color` on native `<select>` to avoid dark-mode contrast bugs on Windows.

## Performance

- **Device/browser matrix.** Test iOS Low Power Mode & macOS Safari.
- **Measure reliably.** Disable extensions that add overhead or change runtime behavior.
- **Track re-renders.** Minimize & make re-renders fast. Use React DevTools or React Scan.
- **Throttle when profiling.** Test with CPU & network throttling.
- **Minimize layout work.** Batch reads/writes; avoid unnecessary reflows/repaints.
- **Network latency budgets.** `POST/PATCH/DELETE` complete in <500ms.
- **Keystroke cost.** Prefer uncontrolled inputs; make controlled loops cheap.
- **Large lists.** Virtualize large lists (e.g., virtua) or use `content-visibility: auto`.
- **Preload wisely.** Preload only above-the-fold images; lazy-load the rest.
- **No image-caused CLS.** Set explicit image dimensions & reserve space.
- **Preconnect to origins.** Use `<link rel="preconnect">` for asset/CDN domains.
- **Preload fonts.** For critical text to avoid flash & layout shift.
- **Subset fonts.** Ship only the code points/scripts you use via unicode-range.
- **Don't use the main thread for expensive work.** Move long tasks to Web Workers.

## Design

- **Layered shadows.** Mimic ambient + direct light with at least two layers.
- **Crisp borders.** Combine borders & shadows; semi-transparent borders improve edge clarity.
- **Nested radii.** Child radius <= parent radius & concentric so curves align.
- **Hue consistency.** On non-neutral backgrounds, tint borders/shadows/text toward the same hue.
- **Accessible charts.** Use color-blind-friendly palettes.
- **Minimum contrast.** Prefer APCA over WCAG 2 for more accurate perceptual contrast.
- **Interactions increase contrast.** `:hover`, `:active`, `:focus` have more contrast than rest state.
- **Browser UI matches your background.** Set `<meta name="theme-color">` to align with the page background.
- **Set the appropriate color-scheme.** Style `<html>` with `color-scheme: dark` in dark themes so scrollbars and device UI have proper contrast.
- **Text anti-aliasing & transforms.** Prefer animating a wrapper instead of the text node. If artifacts persist, use `translateZ(0)` or `will-change: transform`.
- **Avoid gradient banding.** Fading content to dark colors using CSS masks can cause banding. Background images can be used instead.

---
> Source: [markerikson/opencode-config-example](https://github.com/markerikson/opencode-config-example) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
