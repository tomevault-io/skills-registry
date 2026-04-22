---
name: uiux-guidelines
description: UI/UX best practices and guidelines for professional web applications Use when this capability is needed.
metadata:
  author: hermeticormus
---

# UI/UX Guidelines

## Icons vs Emojis

**Always prefer SVG icons over emojis for professional applications.**

### Why Icons Over Emojis

| Aspect | Icons | Emojis |
|--------|-------|--------|
| **Consistency** | Same appearance across all platforms | Vary wildly between OS/browsers |
| **Professionalism** | Clean, professional look | Casual, informal appearance |
| **Customization** | Size, color, stroke width adjustable | Fixed appearance |
| **Accessibility** | Better screen reader support | Inconsistent alt text |
| **Performance** | Lightweight SVG | Font-dependent rendering |
| **Branding** | Match design system colors | Cannot be customized |

### SVG Icon Pattern (React/Next.js)

```tsx
// Define icons as React elements
const Icons = {
  user: (
    <svg className="w-6 h-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
      <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
    </svg>
  ),
  calendar: (
    <svg className="w-6 h-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
      <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
    </svg>
  ),
  // ... more icons
};

// Usage in components
<div className="w-10 h-10 rounded-lg bg-gradient-to-br from-blue-500 to-cyan-500 flex items-center justify-center">
  {Icons.user}
</div>
```

### Recommended Icon Sources

1. **Heroicons** (https://heroicons.com) - Tailwind CSS official icons
2. **Lucide** (https://lucide.dev) - Fork of Feather icons
3. **Radix Icons** (https://icons.radix-ui.com) - Minimal, consistent
4. **Phosphor** (https://phosphoricons.com) - Flexible icon family

### Icon Sizing Guidelines

| Context | Size Class | Pixels |
|---------|------------|--------|
| Inline text | `w-4 h-4` | 16px |
| Button/badge | `w-5 h-5` | 20px |
| Card icon | `w-6 h-6` | 24px |
| Feature icon | `w-8 h-8` | 32px |
| Hero icon | `w-12 h-12` | 48px |

### Icon Container Pattern

```tsx
// Gradient background with white icon
<div className="w-12 h-12 rounded-xl bg-gradient-to-br from-blue-500 to-cyan-500 flex items-center justify-center shadow-md">
  <svg className="w-6 h-6 text-white" ...>
</div>

// Light background with colored icon
<div className="w-10 h-10 rounded-lg bg-blue-100 flex items-center justify-center">
  <svg className="w-5 h-5 text-blue-600" ...>
</div>
```

## General UI Principles

1. **Consistency** - Use the same patterns throughout the app
2. **Hierarchy** - Clear visual hierarchy with size, color, spacing
3. **Feedback** - Visual feedback for all interactions
4. **Accessibility** - WCAG 2.1 AA compliance minimum
5. **Responsiveness** - Mobile-first, fluid layouts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hermeticormus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
