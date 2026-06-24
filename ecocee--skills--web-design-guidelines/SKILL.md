---
name: web-design-guidelines
description: > Use when this capability is needed.
metadata:
  author: ecocee
---

# Web Interface Guidelines Reviewer

A comprehensive UI code review skill that audits frontend files against
established Web Interface Guidelines. Covers accessibility, performance,
typography, animation, forms, theming, and interaction patterns.

Compatible with: Claude, Claude Code, VS Code agent extensions, Cursor,
Windsurf, and any agent that supports the Agent Skills standard.

---

## Behaviour

1. If the user provides a file path or glob pattern, begin the review immediately.
2. If no files are specified, ask: "Which files or patterns would you like me to review?"
3. Fetch the latest guidelines from the source URL before every review — never use a cached version.
4. Read and parse all matched files.
5. Check every file against all rule categories below.
6. Output findings in the `file:line` format defined in the Output Format section.
7. If a file passes all checks, output `✓ pass` — never skip it silently.

---

## Guidelines Source

Always fetch the authoritative, up-to-date rules before reviewing:

```
https://raw.githubusercontent.com/vercel-labs/web-interface-guidelines/main/command.md
```

Use a WebFetch or equivalent tool to retrieve this file fresh on every invocation.
The fetched content is the source of truth. Rules below are a local reference
and may lag behind the canonical source.

---

## Rule Categories (Local Reference)

### Accessibility

- Icon-only buttons require `aria-label`
- Form controls require `<label>` or `aria-label`
- Interactive elements require keyboard handlers (`onKeyDown` / `onKeyUp`)
- Use `<button>` for actions, `<a>` / `<Link>` for navigation — not `<div onClick>`
- Images require `alt`; decorative images use `alt=""`
- Decorative icons require `aria-hidden="true"`
- Async updates (toasts, validation messages) require `aria-live="polite"`
- Prefer semantic HTML over ARIA where a native element exists
- Headings must be hierarchical `<h1>`–`<h6>`; include a skip link for main content
- Heading anchors need `scroll-margin-top`

### Focus States

- All interactive elements need a visible focus indicator (`focus-visible:ring-*` or equivalent)
- Never use `outline-none` / `outline: none` without a replacement focus style
- Use `:focus-visible` over `:focus` to avoid showing ring on mouse click
- Use `:focus-within` for compound controls

### Forms

- Inputs need `autocomplete` and a meaningful `name` attribute
- Use correct `type` (`email`, `tel`, `url`, `number`) and `inputmode`
- Never block paste (`onPaste` + `preventDefault`)
- Labels must be clickable via `htmlFor` or by wrapping the control
- Disable spellcheck on emails, codes, and usernames (`spellCheck={false}`)
- Checkbox and radio label + control share a single hit target — no dead zones
- Submit button stays enabled until the request starts; show spinner during request
- Inline errors displayed next to the relevant field; focus first error on submit
- Placeholders end with `…` and show an example pattern
- Use `autocomplete="off"` on non-auth fields to avoid password manager conflicts
- Warn before navigation when unsaved changes exist (`beforeunload` or router guard)

### Animation

- Respect `prefers-reduced-motion` — provide a reduced variant or disable animation entirely
- Only animate `transform` and `opacity` (compositor-friendly properties)
- Never use `transition: all` — list properties explicitly
- Set `transform-origin` correctly
- SVG: apply transforms on a `<g>` wrapper with `transform-box: fill-box; transform-origin: center`
- Animations must be interruptible — respond to user input mid-animation

### Typography

- Use `…` (ellipsis character), not three periods `...`
- Use curly quotes `"` `"`, not straight quotes `"`
- Non-breaking spaces for units and key combos: `10&nbsp;MB`, `⌘&nbsp;K`, brand names
- Loading states end with `…`: `"Loading…"`, `"Saving…"`
- Apply `font-variant-numeric: tabular-nums` to number columns and comparisons
- Use `text-wrap: balance` or `text-pretty` on headings to prevent widows

### Content Handling

- Text containers must handle long content: `truncate`, `line-clamp-*`, or `break-words`
- Flex children need `min-w-0` to allow text truncation
- Handle empty states explicitly — never render broken UI for empty strings or arrays
- User-generated content: test with short, average, and very long inputs

### Images

- `<img>` requires explicit `width` and `height` to prevent Cumulative Layout Shift (CLS)
- Below-fold images: `loading="lazy"`
- Above-fold critical images: `priority` or `fetchpriority="high"`

### Performance

- Lists with more than 50 items must be virtualized (`virtua`, `content-visibility: auto`)
- No layout reads during render (`getBoundingClientRect`, `offsetHeight`, `offsetWidth`, `scrollTop`)
- Batch DOM reads and writes; never interleave them
- Prefer uncontrolled inputs; controlled inputs must execute cheaply per keystroke
- Add `<link rel="preconnect">` for CDN and asset domains
- Critical fonts: `<link rel="preload" as="font">` with `font-display: swap`

### Navigation and State

- URL must reflect application state — filters, tabs, pagination, and expanded panels belong in query params
- Links must use `<a>` / `<Link>` to support Cmd/Ctrl+click and middle-click
- Deep-link all stateful UI — if a value uses `useState`, consider URL sync via `nuqs` or similar
- Destructive actions require a confirmation modal or undo window — never execute immediately

### Touch and Interaction

- Set `touch-action: manipulation` to prevent double-tap zoom delay
- Set `-webkit-tap-highlight-color` intentionally
- Set `overscroll-behavior: contain` in modals, drawers, and sheets
- During drag: disable text selection; apply `inert` on dragged elements
- Use `autoFocus` sparingly — desktop only, single primary input; avoid on mobile

### Safe Areas and Layout

- Full-bleed layouts need `env(safe-area-inset-*)` for notch/island handling
- Prevent unwanted scrollbars: `overflow-x-hidden` on containers; fix content overflow
- Use Flexbox or Grid for layout instead of JS measurement

### Dark Mode and Theming

- Apply `color-scheme: dark` to `<html>` for dark themes (fixes scrollbars and native inputs)
- `<meta name="theme-color">` must match the page background colour
- Native `<select>`: set explicit `background-color` and `color` (required for Windows dark mode)

### Locale and i18n

- Dates and times: use `Intl.DateTimeFormat` — no hardcoded format strings
- Numbers and currency: use `Intl.NumberFormat` — no hardcoded format strings
- Detect language via `Accept-Language` / `navigator.languages`, not by IP address

### Hydration Safety

- Controlled inputs with `value` require `onChange` (or use `defaultValue` for uncontrolled)
- Date and time rendering: guard against server/client hydration mismatch
- Use `suppressHydrationWarning` only where it is genuinely necessary and document why

### Hover and Interactive States

- Buttons and links need a `hover:` state for visual feedback
- Hover, active, and focus states must be more visually prominent than the resting state

### Content and Copy

- Use active voice: "Install the CLI" not "The CLI will be installed"
- Title Case for headings and button labels (Chicago Manual of Style)
- Use numerals for counts: "8 deployments" not "eight deployments"
- Button labels must be specific: "Save API Key" not "Continue"
- Error messages must include a fix or next step — not just the problem
- Use second person; avoid first person ("you" not "I" or "we")
- Use `&` over "and" where space is constrained

---

## Anti-Patterns (Always Flag)

| Anti-Pattern | Reason |
|---|---|
| `user-scalable=no` or `maximum-scale=1` | Disables user zoom — accessibility violation |
| `onPaste` + `preventDefault` | Prevents users from pasting — never acceptable |
| `transition: all` | Causes unintended transitions and performance issues |
| `outline-none` without focus-visible replacement | Removes all keyboard focus visibility |
| Inline `onClick` navigation without `<a>` | Breaks Cmd/Ctrl+click, middle-click, and right-click |
| `<div>` or `<span>` with click handlers | Must be `<button>` or `<a>` |
| `<img>` without `width` and `height` | Causes layout shift (CLS) |
| Large arrays `.map()` without virtualization | Freezes the browser on large data sets |
| Form inputs without labels | Screen readers cannot identify the field |
| Icon buttons without `aria-label` | Screen readers announce nothing meaningful |
| Hardcoded date or number formats | Breaks for non-English locales |
| `autoFocus` without justification | Disruptive on mobile; confusing for screen reader users |

---

## Output Format

Group findings by file. Use `file:line` format (clickable in VS Code and compatible editors).
Be terse — state the issue and location. Skip explanation unless the fix is non-obvious.
No preamble. No summary paragraph. Start directly with the first file heading.

```
## src/components/Button.tsx

src/components/Button.tsx:42  icon button missing aria-label
src/components/Button.tsx:18  input lacks label association
src/components/Button.tsx:55  animation missing prefers-reduced-motion guard
src/components/Button.tsx:67  transition: all → list properties explicitly

## src/components/Modal.tsx

src/components/Modal.tsx:12   missing overscroll-behavior: contain
src/components/Modal.tsx:34   "..." → "…" (ellipsis character)
src/components/Modal.tsx:89   <div onClick> → <button>

## src/components/Card.tsx

✓ pass
```

---

## Examples

### Example 1: Single file review

User: "Review src/components/LoginForm.tsx"
Agent: Fetches guidelines, reads the file, outputs findings grouped under `## src/components/LoginForm.tsx`.

### Example 2: Glob pattern review

User: "Audit all components in src/components/"
Agent: Fetches guidelines, reads all matched files, outputs findings grouped by file.

### Example 3: Focused accessibility audit

User: "Check my UI for accessibility issues only"
Agent: Asks for the file or pattern, fetches guidelines, then applies only the Accessibility and Focus States rule categories.

### Example 4: No files provided

User: "Review my UI"
Agent: Responds — "Which files or patterns would you like me to review? For example: `src/components/*.tsx` or `src/app/page.tsx`"

---

## Notes

- This skill is agent-agnostic and works in Claude, Claude Code, VS Code, Cursor, Windsurf, Antigravity, and any environment supporting the Agent Skills standard.
- Always fetch the guidelines source URL before reviewing — never rely solely on the local reference above.
- If the fetch fails, inform the user and proceed using the local reference rules as a fallback, noting that the review may not reflect the latest guidelines.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ecocee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
