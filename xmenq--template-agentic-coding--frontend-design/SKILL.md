---
name: frontend-design
description: Guidelines for building UI — components, layouts, responsive design, accessibility, and visual polish Use when this capability is needed.
metadata:
  author: xmenq
---

# Frontend Design Skill

## When to use this skill

Use when building or modifying any user-facing interface: pages, components, layouts, forms, navigation, modals, or visual design.

---

## Design Principles

### 1. Mobile-first, responsive always
- Design for the smallest screen first, enhance for larger screens
- Use responsive breakpoints consistently:
  - `sm`: 640px | `md`: 768px | `lg`: 1024px | `xl`: 1280px
- Test every UI change at all breakpoints before submitting

### 2. Visual hierarchy matters
- Use size, weight, color, and spacing to guide the user's eye
- Primary actions should be the most visually prominent
- Limit color palette to 2-3 primary colors + neutrals
- Use whitespace generously — don't crowd elements

### 3. Consistency over creativity
- Reuse existing components before creating new ones
- Follow the established design tokens (colors, spacing, typography)
- Match existing interaction patterns (how buttons, forms, modals work)

### 4. Accessibility is not optional
- All interactive elements must be keyboard navigable
- Images need `alt` text, icons need `aria-label`
- Color contrast ratio: minimum 4.5:1 for normal text, 3:1 for large text
- Form inputs need associated labels
- Test with screen reader if possible

---

## Component Architecture

### Structure
```
ui/
├── components/     # Reusable UI components
│   ├── Button/
│   │   ├── Button.[ext]
│   │   ├── Button.styles.[ext]
│   │   └── Button.test.[ext]
│   └── ...
├── layouts/        # Page layout templates
├── pages/          # Full page components
└── hooks/          # UI-specific hooks/utilities
```

### Component rules
- **One component per file** — no bundling unrelated components
- **Co-locate styles** — styles live next to their component
- **Co-locate tests** — test files next to what they test
- **Props are the contract** — type all props explicitly
- **No business logic in components** — components render, services compute

### Component checklist
For every new component:
- [ ] Props are typed with clear names
- [ ] Has sensible defaults for optional props
- [ ] Handles loading, empty, and error states
- [ ] Responsive at all breakpoints
- [ ] Keyboard accessible
- [ ] Tested with at least 3 cases (default, edge, error)

---

## Layout Patterns

### Common layouts
| Pattern | When to use |
|---------|------------|
| **Single column** | Simple content pages, forms, articles |
| **Sidebar + content** | Dashboards, settings, admin panels |
| **Grid** | Card listings, galleries, product catalogs |
| **Split screen** | Comparison views, editor + preview |
| **Stacked header/content/footer** | Marketing pages, landing pages |

### Spacing system
Use a consistent spacing scale (multiples of a base unit):
```
4px → 8px → 12px → 16px → 24px → 32px → 48px → 64px → 96px
```

---

## Forms

### Rules
- Every input has a visible label (not just placeholder text)
- Show validation errors inline, next to the relevant field
- Disable submit button while processing (with loading indicator)
- Preserve user input on validation failure (don't clear the form)
- Use appropriate input types: `email`, `tel`, `number`, `date`, etc.

### Validation UX
1. Validate on blur (when user leaves the field)
2. Re-validate on change after first error
3. Show success state (green check) after correction
4. Summarize errors at the top if there are multiple

---

## Animations & Transitions

### Rules
- **Subtle > flashy** — animations should feel natural, not distracting
- **Fast** — most transitions should be 150-300ms
- **Purposeful** — every animation should communicate something (appearing, moving, feedback)
- **Respect user preferences** — honor `prefers-reduced-motion`

### Common transitions
| Action | Animation | Duration |
|--------|-----------|----------|
| Page/section enter | Fade in + slight slide up | 200ms |
| Modal open | Fade in + scale from 95% | 200ms |
| Hover feedback | Color/shadow change | 150ms |
| Loading state | Skeleton shimmer or spinner | Continuous |
| Success feedback | Brief green flash or checkmark | 300ms |
| Error feedback | Brief red highlight + shake | 300ms |

---

## Dark Mode

If the project supports dark mode:
- Use CSS custom properties (variables) for all colors
- Define a `light` and `dark` theme set
- Never hardcode colors — always reference tokens
- Test all components in both themes
- Respect `prefers-color-scheme` system setting

---

## Performance

- **Lazy load** images and heavy components below the fold
- **Optimize images** — use WebP/AVIF, appropriate sizes, responsive srcset
- **Minimize layout shifts** — set explicit dimensions on images/videos
- **Bundle size** — monitor and limit JS/CSS bundle sizes
- **Critical CSS** — inline above-the-fold styles when possible

---

## PR Checklist for UI Changes

- [ ] Looks correct at all breakpoints (mobile, tablet, desktop)
- [ ] Keyboard navigation works
- [ ] Loading, empty, and error states are handled
- [ ] Colors meet contrast requirements
- [ ] Animations respect `prefers-reduced-motion`
- [ ] No hardcoded colors/sizes (uses design tokens)
- [ ] Screenshot or video attached to PR
- [ ] Dark mode tested (if applicable)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xmenq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
