---
name: responsive-check
description: | Use when this capability is needed.
metadata:
  author: dayoumin
---

# Responsive Design Check Skill

Mobile-first responsive design verification for Tailwind CSS projects.

## When to Activate

- User mentions "반응형", "모바일", "responsive", "mobile", "breakpoint"
- Reviewing layout components
- Testing across screen sizes

## Breakpoint Reference

| Name | Tailwind | Range | Characteristics |
|------|----------|-------|-----------------|
| **Mobile** | default | <640px | Single column, touch UI |
| **Small** | `sm:` | ≥640px | Extended mobile |
| **Medium** | `md:` | ≥768px | Tablet, 2-column possible |
| **Large** | `lg:` | ≥1024px | Desktop, sidebar |
| **XL** | `xl:` | ≥1280px | Wide desktop |
| **2XL** | `2xl:` | ≥1536px | Large monitors |

## Checklist

### 1. Layout

#### Container
- [ ] Max-width constraints (`max-w-6xl`, `max-w-7xl`)
- [ ] Center alignment (`mx-auto`)
- [ ] Responsive padding (`px-4 md:px-6 lg:px-8`)

#### Grid/Flex
- [ ] Mobile: single column
- [ ] Tablet: 2-column (`md:grid-cols-2`)
- [ ] Desktop: multi-column (`lg:grid-cols-3`)
- [ ] `flex-col md:flex-row` pattern

#### Navigation
- [ ] Mobile: hamburger menu or bottom nav
- [ ] Desktop: sidebar or top nav
- [ ] `hidden md:flex` / `md:hidden` pattern

### 2. Typography

#### Responsive Font Sizes
- [ ] Headings: `text-xl md:text-2xl lg:text-3xl`
- [ ] Body: default 16px (no change needed)
- [ ] Small text: `text-xs sm:text-sm`

#### Text Visibility
- [ ] Long text truncated on mobile
- [ ] `truncate` or `line-clamp-*` usage
- [ ] `hidden sm:inline` pattern

### 3. Interactive Elements

#### Touch Targets
- [ ] Minimum 44x44px (buttons, links)
- [ ] Sufficient spacing (8px+)
- [ ] Keep `p-2` not `p-2 md:p-1`

#### Hover vs Touch
- [ ] Consider `@media (hover: hover)`
- [ ] Touch alternatives for hover effects
- [ ] Clear click/tap feedback

### 4. Images/Media

#### Responsive Images
- [ ] `w-full` or `max-w-full`
- [ ] Proper `object-cover` / `object-contain`
- [ ] srcset or Next.js Image component

#### Aspect Ratio
- [ ] Use `aspect-video`, `aspect-square`
- [ ] Avoid fixed heights (`h-[300px]`)

### 5. Show/Hide Patterns

```tsx
// ✅ Correct
<div className="hidden md:block">Desktop only</div>
<div className="md:hidden">Mobile only</div>

// ❌ Incorrect (JS-dependent)
<div style={{display: isMobile ? 'block' : 'none'}}>
  // SEO/accessibility issues
</div>
```

## Report Format

```markdown
# 📱 Responsive Design Report

## Summary
- **Mobile**: ✅/⚠️/❌
- **Tablet**: ✅/⚠️/❌
- **Desktop**: ✅/⚠️/❌

## Issues by Breakpoint

### Mobile (<768px)
- [Issue] description

### Tablet (768-1024px)
- [Issue] description

### Desktop (>1024px)
- [Issue] description

## Recommendations
[Improvement suggestions]
```

## Project-Specific Patterns

This project uses:
- **Sidebar**: `hidden md:flex` (hidden on mobile)
- **MobileNav**: `md:hidden` (hidden on desktop)
- **Container**: `max-w-6xl mx-auto w-full px-6`
- **Sidebar collapse**: icons only on tablet

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dayoumin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
