---
name: align-design
description: Align React component styling to match HTML mockup pixel-for-pixel. Reads mockup CSS, compares with component, fixes all differences. Use when user wants to match a component to its mockup or mentions /align-design command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /align-design - Align Component to Mockup

Update a React component's appearance to match its HTML mockup exactly.

## Usage
```
/align-design "Registration"
/align-design 02                       # By story number
/align-design RegistrationPage.tsx     # By component file
/align-design                          # Interactive selection
```

## Phase 1: Locate Files

1. **Find mockup**: `ProductSpecification/Stories/NN-story-name/mockups/desktop/01-*.html`
   - Note: folder may be `Stories` (capital S) or `stories` (lowercase). Check both.
2. **Find component**: `frontend/src/features/{feature}/components/{Feature}Page.tsx`
3. If multiple mockups exist, ask user which screen state to align to.

## Phase 2: Extract Mockup Design Tokens

Read the mockup HTML carefully. Extract EVERY CSS value into a checklist:

### Page Layout
- [ ] Body/page background color
- [ ] Container max-width
- [ ] Centering method and padding

### Card/Container
- [ ] Background color
- [ ] Border (presence, color, width)
- [ ] Border radius
- [ ] Padding (all sides)
- [ ] Box shadow (exact value)

### Typography
- [ ] Logo: font-size, font-weight, letter-spacing, color, accent color
- [ ] Title: font-size, font-weight, color, margin-bottom
- [ ] Subtitle: font-size, color
- [ ] Labels: font-size, font-weight, color, margin-bottom
- [ ] Helper/muted text: font-size, color
- [ ] Optional markers: font-size, font-weight, color

### Form Inputs
- [ ] Padding (vertical and horizontal)
- [ ] Font size
- [ ] Border: color, width, radius
- [ ] Background color
- [ ] Placeholder color
- [ ] Focus state: border color, ring/shadow color and size

### Buttons
- [ ] Padding (vertical and horizontal)
- [ ] Font size, font weight
- [ ] Border radius
- [ ] Background color, hover color
- [ ] Text color
- [ ] Hover shadow
- [ ] Active state (transform)

### Spacing
- [ ] Gap between form groups (margin-bottom)
- [ ] Gap between label and input
- [ ] Spacing around dividers
- [ ] Section margins

### Special Elements
- [ ] Password toggle: position, colors, hover state
- [ ] Checkbox: size, accent color
- [ ] Divider: line color, text background, width calculation
- [ ] Links: color, hover decoration, font-weight
- [ ] Icons: size, color, stroke vs fill

## Phase 3: Compare and Fix

For EACH item in the checklist, compare mockup value vs component value:

### Common Mismatches to Watch For

| Issue | Mockup | Typical React/shadcn Default |
|-------|--------|------------------------------|
| Page bg | `#fafbfc` (light gray) | `#ffffff` (white) |
| Card | No border, large padding | Has border, small padding |
| Input bg | White `#ffffff` | Gray `#f3f3f5` (input-background) |
| Input height | `padding: 12px 16px` (tall) | `h-9` / 36px (short) |
| Button height | `padding: 14px 24px` (tall) | `h-9` / 36px (short) |
| Focus ring | Green glow `#d1fae5` | Default ring color |
| Border radius | `8px` / `12px` | `0.625rem` / `calc(var(--radius))` |
| Font sizes | Exact px values | Tailwind defaults |

### Approach

- **Prefer native HTML elements** (`<input>`, `<button>`, `<label>`) with explicit Tailwind classes over shadcn components when shadcn defaults conflict with the mockup.
- **Keep shadcn components** only when their styling matches (e.g., `Checkbox` with Radix primitives).
- Use **exact hex colors** from mockup CSS variables, not Tailwind color names.
- Use **exact pixel/rem values** from mockup, not Tailwind approximations.

## Phase 4: Verify

1. `npm run build` — no errors
2. `npx vitest run` — all tests pass
3. Run Selenium test if one exists — verify `data-testid` attributes still work
4. Visual check: compare in browser side-by-side with mockup

## Key Rules

- **NEVER remove `data-testid` attributes** — Selenium tests depend on them
- **NEVER change component behavior** — only styling
- **Match mockup exactly** — use mockup's CSS values, not "close enough" Tailwind classes
- If mockup and shadcn theme conflict, mockup wins
- Divider, login link, and other elements outside `<form>` in mockup must be outside `<form>` in component

## Story Mapping

| # | Story |
|---|-------|
| 01 | Login/Logout |
| 02 | Registration |
| 03 | Password Reset |
| 04 | Connect Calendar |
| 05 | Create Task |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
