---
name: frontend-ui-ux-design
description: Guide React component design for Fogo using the Fogo design system (Industrial Warmth, dark-first, Fogo amber brand color, Tailwind CSS). Use when designing UI, creating components, styling, or writing frontend code. Use when this capability is needed.
metadata:
  author: fedickinson
---

# Frontend UI/UX Design Skill

## Design Authority

ALWAYS read `DESIGN.md` before any visual work. It is the single source of truth.
Full token definitions: `frontend/tailwind.config.js`
Component patterns and Tailwind recipes: `docs/v2/fogo-mockups/shared-patterns.md`
Mockup index: `docs/v2/fogo-mockups/README.md`

Before building any page: DESIGN.md → `shared-patterns.md` → `{page}-spec.md` → `{page}.html`

## Design System: Fogo (Industrial Warmth)

- Dark theme only, base `#100F12`
- **Fogo amber** (`#D4952A`) = brand color, AI presence, active states. Used ONLY for: active nav, Fogo card borders, action buttons in Fogo flows, logo, Fogo character glow.
- **Teal** (`#7EC8D4`) = data precision, chart lines, current-week highlight, data callouts. NOT for buttons. NOT the brand color.
- These are DIFFERENT. Amber = "Fogo is here." Teal = "look at this number."
- NEVER interchange them — they have distinct, non-overlapping meanings.

## Fonts

- **Satoshi** (`font-display`): display headings, page titles
- **Instrument Sans** (`font-body`): body text, UI labels, buttons
- **Geist Mono** (`font-mono`): financial amounts, percentages, dates, stats

Numbers always in `font-mono`. Never body font for amounts.

## Color Quick Reference

| Purpose | Token | Value |
|---------|-------|-------|
| Brand / AI presence | `fogo-amber` | `#D4952A` |
| Data precision | `teal` | `#7EC8D4` |
| Budget warning | `semantic-caution` | `#D4A06A` |
| Over budget | `semantic-danger` | `#E8605A` |
| Under budget | `semantic-positive` | `#3DCF85` |
| Income | `semantic-income` | `#60C8B0` |
| Expense (default) | `semantic-expense` | `#9E83BC` |
| FIRE progress (month-end only) | `fire-gold` | `#C8922A` |

## Key Component Rules

- **Card radius:** `rounded-lg` (10px) — not bubbly 16px, not stiff 4px
- **Card:** `bg-surface-raised border border-border-subtle rounded-lg shadow-card p-6`
- **Fogo Card:** `bg-surface-raised border border-border-subtle border-l-2 border-l-fogo-amber rounded-r-lg p-5 px-6 shadow-[0_0_50px_rgba(212,149,42,0.05)]`
- **Max content width:** 860px typical, 680px for conversational (Fogo messages)
- **Sidebar:** 200px fixed

## Motion Rules

- No spring physics, no elastic bounce, no confetti
- Max duration: 300ms
- Enter: `ease-out`, Exit: `ease-in`
- Fogo typing indicator: slow pulse (1.4s), `animate-fogo-pulse`

## Fogo Character Placement

| Location | Size | Present? |
|----------|------|----------|
| Dashboard narrative | 140px | YES — amber glow |
| Review (Reflect + Plan) | 160px+ | YES — full emotional range |
| Ask Fogo (chat) | 48px avatar | YES |
| Transactions, Analytics, Budget, Settings | — | NO — absence is intentional |

## Anti-Patterns (Never Do)

- No glassmorphism / backdrop blur on cards
- No gradient buttons
- No spring/elastic animations
- No confetti
- No emoji as design elements in JSX
- No teal on buttons (teal = data only)
- No amber on non-Fogo elements
- No red for expenses (use `semantic-expense` muted purple)

## Surface Hierarchy (in order)

```
bg-surface-base     #100F12  — app shell
bg-surface-raised   #18171B  — default cards
bg-surface-overlay  #201F24  — hover, expanded rows
bg-surface-float    #28272D  — dialogs, popovers, inputs
```

## Component Patterns

See `docs/v2/fogo-mockups/shared-patterns.md` for full recipes with Tailwind classes.
See `docs/v2/fogo-mockups/shared-progress-bars.md` for progress bar variants.
See `docs/v2/fogo-mockups/sidebar-spec.md` for sidebar layout and pulse component.

## Uppercase Labels

UI chrome labels (e.g., `GROCERIES`, `THIS WEEK`) use:
```
font-mono text-data-sm text-text-tertiary uppercase tracking-label
```
where `tracking-label` = `0.08em`.

## Last Updated

2026-04-02 — Updated for Fogo rebrand and new design system

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fedickinson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
