---
name: ui-design
description: UI visual design principles and patterns. Use when designing interfaces, choosing colors, typography, icons, and creating visual hierarchy. Use when this capability is needed.
metadata:
  author: aiyuekuang
---

# UI Design Skill

Visual design principles for creating beautiful, consistent user interfaces.

## When to Use This Skill

- Choosing colors and typography
- Creating visual hierarchy
- Designing icons and graphics
- Building design systems
- Ensuring visual consistency

---

# 🎨 Color Theory

## Color Palette Structure

```
Primary     → Brand identity, main CTAs
Secondary   → Supporting elements
Accent      → Highlights, notifications
Neutral     → Text, backgrounds, borders
Semantic    → Success, Warning, Error, Info
```

## Color Scale (Example: Blue)

```
blue-50   → #eff6ff  → Lightest background
blue-100  → #dbeafe  → Hover states
blue-200  → #bfdbfe  → Borders
blue-300  → #93c5fd  → Disabled
blue-400  → #60a5fa  → Icons
blue-500  → #3b82f6  → Primary (base)
blue-600  → #2563eb  → Primary hover
blue-700  → #1d4ed8  → Primary active
blue-800  → #1e40af  → Dark mode primary
blue-900  → #1e3a8a  → Darkest
```

## Contrast Requirements (WCAG 2.1)

| Level | Normal Text | Large Text |
|-------|-------------|------------|
| AA | 4.5:1 | 3:1 |
| AAA | 7:1 | 4.5:1 |

```
✅ Good: Dark text on light background
   #1f2937 on #ffffff → 16:1

❌ Bad: Low contrast
   #9ca3af on #ffffff → 2.5:1
```

---

# 📝 Typography

## Type Scale

```
Display   → 48-72px  → Hero sections
H1        → 36-48px  → Page titles
H2        → 24-30px  → Section headers
H3        → 20-24px  → Subsections
H4        → 16-18px  → Card titles
Body      → 14-16px  → Paragraph text
Caption   → 12px     → Labels, hints
```

## Font Pairing

```
Headings: Inter, SF Pro, Geist Sans
Body:     Inter, System UI, Roboto
Code:     JetBrains Mono, Fira Code
```

## Line Height

```
Headings:  1.1 - 1.3
Body text: 1.5 - 1.7
UI labels: 1.2 - 1.4
```

## Font Weight

```
Regular (400)  → Body text
Medium (500)   → Emphasis, labels
Semibold (600) → Headings, buttons
Bold (700)     → Strong emphasis
```

---

# 📐 Visual Hierarchy

## Size & Weight

```
Large + Bold   → Most important
Medium + Medium → Secondary
Small + Regular → Tertiary
```

## Spacing Creates Grouping

```
Related items    → 8-16px apart
Separate groups  → 24-32px apart
Sections         → 48-64px apart
```

## Color for Emphasis

```
Primary color   → Call to action
Dark text       → Important content
Gray text       → Secondary info
Light gray      → Disabled/placeholder
```

---

# 🔲 Shadows & Elevation

## Shadow Scale

```css
/* Level 0: Flat */
shadow-none

/* Level 1: Raised (cards, dropdowns) */
shadow-sm: 0 1px 2px rgba(0,0,0,0.05)

/* Level 2: Floating (popovers, tooltips) */
shadow: 0 1px 3px rgba(0,0,0,0.1), 0 1px 2px rgba(0,0,0,0.06)

/* Level 3: Overlay (modals) */
shadow-lg: 0 10px 15px rgba(0,0,0,0.1), 0 4px 6px rgba(0,0,0,0.05)

/* Level 4: Modal */
shadow-xl: 0 20px 25px rgba(0,0,0,0.1), 0 10px 10px rgba(0,0,0,0.04)
```

---

# 🔘 Border Radius

```
None     → 0     → Sharp edges (tables)
Small    → 4px   → Inputs, buttons
Medium   → 8px   → Cards, modals
Large    → 12px  → Large cards
XL       → 16px  → Containers
Full     → 9999px → Pills, avatars
```

---

# 🖼️ Iconography

## Icon Sizes

```
12px → Inline, badges
16px → Body text, inputs
20px → Buttons, navigation
24px → Headers, emphasis
32px → Feature icons
48px → Empty states
```

## Icon Guidelines

- Use consistent stroke width (1.5-2px)
- Maintain optical balance
- Use filled for emphasis, outlined for default
- Ensure touch targets are 44x44px minimum

---

# 📊 Data Visualization Colors

```
Category palette (distinct):
Blue    → #3b82f6
Green   → #22c55e
Orange  → #f97316
Purple  → #a855f7
Pink    → #ec4899

Sequential palette (progression):
Light → Medium → Dark

Semantic:
Success → Green
Warning → Yellow/Orange
Error   → Red
Info    → Blue
```

---

# 📚 Design Resources

- [Tailwind UI](https://tailwindui.com/)
- [shadcn/ui](https://ui.shadcn.com/)
- [Radix Colors](https://www.radix-ui.com/colors)
- [Lucide Icons](https://lucide.dev/)
- [Heroicons](https://heroicons.com/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiyuekuang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
