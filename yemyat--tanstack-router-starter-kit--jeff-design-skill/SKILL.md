---
name: jeff-design-skill
description: Jeff's design language for react-app. Includes the full component-level conventions from the original linear-design skill plus Jeff's-specific direction (typography-first, subtle separators, and strict light/dark parity). Use when building or styling frontend Use when this capability is needed.
metadata:
  author: yemyat
---

# Jeff's Design Skill

This skill intentionally preserves the full original linear-design conventions and adds Jeff's-specific guidance where needed.

## Jeff's-specific additions

- Treat this file as canonical for Jeff's UI decisions in `react-app`.
- Preserve the clean, typography-forward look shown in current Jeff's screens (dashboard, attendance records, expenses sheet).
- Prefer subtle separators and low-contrast structure lines over heavy visual chrome.
- Keep data-dense tables highly scanable with stable alignment and consistent row rhythm.
- Support both light and dark mode with semantic tokens; no hard-coded light-only colors for shared surfaces.

Applies Linear's design sensibility — utilitarian but polished — to Jeff's's react-app. This is not a reskin. The brand colors, fonts, and shadcn primitives stay. This skill governs _how_ they're composed.

## Stack Context

- React 19, Tailwind v4, shadcn (new-york), Radix, Lucide
- Fonts: DM Sans (sans-serif), Instrument Serif (serif), Geist Mono (monospace)
- All numbers, dates, and tabular data use `font-mono tabular-nums` (Geist Mono)
- Brand primary: `#0F3CFF` (vivid blue) — same in both light and dark mode
- Brand secondary: `#FEF3C7` (light gold) — same in both light and dark mode
- Base radius: `0.25rem` (4px) — tight, utilitarian corners. Not rounded, not sharp.
- Sonner toasts must use `var(--radius)` to match the global radius

### Light Mode Palette

- Background: `#FBFBFB` — barely off-white
- Card: pure white `#FFFFFF` — lifts slightly off background
- Popover: `#FEFCF7` — warm cream tint for floating surfaces
- Border: `oklch(0.95)` — nearly invisible, barely-there hint
- Foreground: dark near-black

### Dark Mode Palette (Tokyo Night-inspired)

All dark mode surfaces carry a blue undertone — never flat neutral grey.

- Background: `#1a1b26` — deep navy
- Card: `#1f2335` — slightly lifted surface
- Popover: `#2F2D3A` — warmer purple-grey for floating surfaces
- Popover text: near-white (`oklch(0.95)`) for legibility against the warmer popover bg
- Foreground: `#c0caf5` — soft lavender
- Muted foreground: same as foreground — no dimming in dark mode, text stays legible
- Border: `#3b4261` — subtle blue-grey edges
- Muted/accent surfaces: `#292e42`

## Core Principles

### 1. Soft Borders

- Default border color is `border` (`oklch(0.95)` in light, `#3b4261` in dark). Barely visible — just enough to define edges.
- Use `border-border` at full opacity. The color itself is already subtle enough. No need for `border-border/50` — the theme value handles it.
- **No shadows by default.** Cards, containers, and surfaces use `border` only. No `shadow-sm`, no `shadow-md`. Shadows are reserved for floating elements only (dropdowns, popovers, modals).
- Dividers (`<Separator />`): use `bg-border` — they should whisper, not shout.
- Rounded corners: `0.25rem` base radius — tight and utilitarian. Use the shadcn radius scale (`rounded-md` / `rounded-lg`). Never use `rounded-none` on interactive elements, but don't go overly round either.
- **Never use `rounded-full`** on badges, tags, pills, or any rectangular element unless explicitly requested. Use `rounded-md` instead. The only exception is avatar circles and small dot indicators (`size-2 rounded-full`).

### 2. Clean Layout

- Every page follows a simple vertical rhythm: **page header → content area → (optional) footer**.
- Page header: title left-aligned, actions right-aligned, single row. No stacked headers.
- Content sections: separated by `space-y-6` (24px) between major blocks, `space-y-4` (16px) within a block.
- Cards: `p-5` or `p-6` internal padding. Never less than `p-4`.
- Tables: always edge-to-edge — no horizontal padding or margin wrapping the table. The table itself spans the full width of its container. Only individual cells have internal padding (`px-4 py-3`).
- Sidebar: already handled by shadcn sidebar component — don't override its spacing.
- Avoid nesting cards inside cards. If you need grouped content inside a card, use a subtle `bg-muted/50 rounded-md p-4` section instead.
- No decorative elements. Every pixel earns its place.

### 3. Typography Hierarchy

Use weight and size to create hierarchy, not color variety.

| Role                | Class                                   | Example                    |
| ------------------- | --------------------------------------- | -------------------------- |
| Page title          | `text-lg font-semibold text-foreground` | "Employees"                |
| Section title       | `text-base font-medium text-foreground` | "Personal Information"     |
| Card title          | `text-sm font-medium text-foreground`   | "Leave Balance"            |
| Body text           | `text-sm text-foreground`               | Normal content             |
| Secondary text      | `text-sm text-muted-foreground`         | Descriptions, timestamps   |
| Caption / hint      | `text-xs text-muted-foreground`         | Helper text, table footers |
| Data value (large)  | `text-2xl font-semibold font-mono tabular-nums` | "142"               |
| Data value (inline) | `text-sm font-medium font-mono tabular-nums`    | "12.5 days"         |

- Headings: always use `text-balance`.
- Body paragraphs: always use `text-pretty`.
- Numbers/data: always use `font-mono tabular-nums` so columns align and render in Geist Mono.
- Never use `font-bold` — `font-semibold` is the heaviest weight in this system.
- Never use `text-xl` or larger for anything other than a dashboard stat or empty state illustration text.

### 4. White Space

Linear's defining trait: things have room to breathe.

- Page-level horizontal padding: `px-6` minimum on desktop.
- Between page title and first content block: `mt-6`.
- Between cards in a grid: `gap-4` or `gap-6`.
- Inside form groups: `space-y-4` between fields, `space-y-1.5` between label and input.
- Between a section title and its content: `mt-3`.
- Button groups: `gap-2` between buttons.
- Empty states: generous `py-16` vertical padding, centered content.
- When in doubt, add more space. Cramped layout is the most common violation.

## Component Conventions

### Buttons

- Primary action: `<Button>` (default variant, vivid blue `#0F3CFF`).
- Secondary action: `<Button variant="secondary">` (light gold `#FEF3C7`).
- Outline: `<Button variant="outline">` for neutral actions.
- Destructive: `<Button variant="destructive">`.
- Ghost buttons for tertiary / icon-only actions.
- Size: default size for page actions, `size="sm"` inside tables and inline contexts.
- Icon + label buttons: icon on the left, `gap-2`, icon size `size-4`.

### Cards

```
<Card>
  <CardHeader className="pb-3">
    <CardTitle className="text-sm font-medium">Title</CardTitle>
    <CardDescription>Optional subtitle</CardDescription>
  </CardHeader>
  <CardContent>...</CardContent>
</Card>
```

- No shadows. The Card component uses `border` only.
- `pb-3` on CardHeader to tighten the gap between header and content (shadcn default is too loose).

### Tables

- Use the shadcn `<DataTable />` component (wraps TanStack Table).
- **Edge-to-edge**: tables never have wrapper padding. They stretch to the full width of their parent. No `px-*` on the table's container div. If the page has `px-6`, the table breaks out of it or lives outside the padded area.
- **Breathable rows**: cells use `px-4 py-3` — never cramped. Header height is `h-10`. Rows should feel spacious, not like a spreadsheet.
- Header row: `text-[11px] font-semibold text-muted-foreground uppercase tracking-wide`.
- Row hover: `hover:bg-muted/30` — subtle highlight, no color shift.
- No zebra striping. Clean rows with `border-b border-border`.
- Action column: right-aligned, ghost icon buttons only.
- **Sticky columns**: for wide tables with horizontal scroll, pin key identifier columns (e.g., employee name) using `pinnedColumns={{ left: ["columnId"] }}` on `<DataTable />`. Pinned cells get `position: sticky` with `bg-card` to prevent see-through on scroll.
- **Grouped column headers**: use TanStack Table column groups (`columns` array inside a parent column def) for tables with logical groupings (e.g., Scheduled, Attendance, Leave). Group headers are left-aligned by default. Separate each group visually with a left border on the group header and its first child column using `meta: { className: "border-l border-border" }`. Sub-column headers and data cells within groups should be centered using a `centeredHeader()` helper and `text-center` on cell wrappers. The `DataTable` component supports `meta.className` on any column def — the class is applied to both `<th>` and `<td>` elements.

### Forms

- Labels: `text-sm font-medium text-foreground`.
- Inputs: use shadcn `<Input />` as-is — the border and radius already match.
- Field spacing: `space-y-4` between fields.
- Label-to-input gap: `space-y-1.5`.
- Error messages: `text-xs text-destructive mt-1`.
- Form actions (submit/cancel): right-aligned, primary action on the right.

### Empty States

- Center-aligned in the content area.
- Lucide icon at `size-10 text-muted-foreground/50`.
- Title: `text-sm font-medium text-foreground`.
- Description: `text-sm text-muted-foreground max-w-sm mx-auto`.
- Single CTA button below.
- Generous vertical padding: `py-16`.

### Status Indicators

- Use small colored dots (`size-2 rounded-full`) or subtle badges.
- Badge style: `text-xs font-medium px-2 py-0.5 rounded-md` with muted background tints.
- Avoid heavy colored backgrounds. Prefer `bg-green-50 text-green-700` style tints over solid fills.

## Anti-Patterns (Never Do These)

- Any shadows on cards or containers (`shadow-sm`, `shadow-md`, `shadow-lg`). Shadows are only for floating overlays (dropdowns, popovers, modals)
- Gradient backgrounds or gradient text
- Colored backgrounds on page sections (keep it `bg-background` or `bg-card`)
- Bold borders (`border-2` or darker colors)
- Icon overload — if a label is clear, skip the icon
- Centered page titles (always left-align)
- Multiple font sizes in a single line of text
- Using color alone to convey meaning (always pair with text or icon)
- Cramming content — if it feels tight, it is tight. Add space.

## Decision Shortcuts

| Question                       | Answer                                                                                                 |
| ------------------------------ | ------------------------------------------------------------------------------------------------------ |
| Border or shadow?              | Border only. No shadows on static surfaces. Shadows reserved for floating elements (dropdowns, modals) |
| How much padding?              | Start with `p-5`, adjust to `p-4` only if space-constrained                                            |
| What font weight for emphasis? | `font-medium`. Reserve `font-semibold` for titles only                                                 |
| Colored or grey icon?          | Grey (`text-muted-foreground`). Colored only for status                                                |
| Big stat number size?          | `text-2xl font-semibold tabular-nums`                                                                  |
| Table border style?            | `border-b border-border/50` on rows. No vertical borders                                               |
| Loading state?                 | Skeleton shimmer. Never spinners in content areas                                                      |
| Notification/toast position?   | Bottom-right                                                                                           |

## Dark/Light Mode Requirements (Jeff's)

- Every new/reworked UI must be checked in both themes before completion.
- Use semantic tokens (`bg-background`, `bg-card`, `text-foreground`, `text-muted-foreground`, `border-border`) for shared surfaces.
- Dark mode is Tokyo Night-inspired: all surfaces have a blue undertone. Never use flat neutral greys (`oklch(x 0 0)`) for dark mode backgrounds, borders, or muted surfaces.
- `muted-foreground` in dark mode is the same value as `foreground` — don't rely on it for dimming text. Use opacity (`text-foreground/70`) if you need subtle text in dark mode.
- Popover surfaces are intentionally warmer (purple-grey) than cards in dark mode — they should feel elevated.
- Primary (`#0F3CFF`) and secondary (`#FEF3C7`) are identical across light and dark mode.
- Ensure dividers, badges, table headers, and disabled states remain legible in dark mode.
- Avoid hard-coded grayscale values for foundational layout surfaces.

## Jeff's Review Checklist

- [ ] Visual structure is created with subtle separators, not heavy shadows.
- [ ] Typography hierarchy is clear and intentional in dense screens.
- [ ] Tables remain readable at high data density (especially attendance/expenses style screens).
- [ ] Sheet/dialog forms keep compact but breathable spacing.
- [ ] UI is verified in both light and dark mode.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yemyat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
