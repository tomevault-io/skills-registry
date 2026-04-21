---
name: theme-marketplace-skill
description: Theme and skin marketplace for real_deal platform including theme development, style token architecture, theme validation, performance requirements, accessibility standards, and creator revenue sharing. Use when developing themes, managing the theme store, validating theme submissions, enforcing performance/accessibility standards, or handling theme monetization. Use when this capability is needed.
metadata:
  author: phuhao00
---

# Theme/Skin Marketplace

## Theme Architecture

### Style Token System
```
tokens/
  colors/          - Color palette (primary, secondary, neutral, semantic)
  typography/      - Font families, sizes, weights, line heights
  spacing/         - Spacing scale (0-8xl)
  borders/         - Border radius, widths
  shadows/         - Shadow definitions
  transitions/     - Animation timing functions
```

### Theme Structure
```css
/* themes/dark-mode.css */
:root {
  --color-primary: #6366f1;
  --color-bg: #0f172a;
  --color-text: #f8fafc;
  /* ... */
}
```

### Component Theming
- Base components accept theme tokens
- CSS custom properties for dynamic theming
- Dark/light mode support
- Custom theme upload support

## Theme Development

### Theme Components
1. **Color Scheme**: Primary, secondary, accent colors
2. **Typography**: Fonts, headings, body text
3. **Layout**: Spacing, grid, breakpoints
4. **UI Elements**: Buttons, cards, modals, navigation
5. **Animations**: Transitions, micro-interactions

### Theme Guidelines
- Follow WCAG 2.1 AA contrast ratios (4.5:1)
- Support responsive design
- Provide light and dark variants
- Include preview images
- Document color/typography choices

## Validation Requirements

### Performance Standards
- **CSS Size**: < 50KB uncompressed
- **First Contentful Paint**: < 1.8s
- **Time to Interactive**: < 3.8s
- **Animation Performance**: 60fps minimum

### Accessibility Standards
- WCAG 2.1 AA compliance
- Keyboard navigation support
- Screen reader compatibility
- Focus indicators
- Reduced motion preference

### Theme Validation Checklist
- [ ] Contrast ratios meet WCAG AA
- [ ] All interactive elements keyboard accessible
- [ ] Reduced motion respected
- [ ] Dark mode variant included
- [ ] Responsive breakpoints defined
- [ ] CSS optimized (no unused styles)
- [ ] No !important overrides
- [ ] Compatible with base component system
- [ ] Includes theme preview images
- [ ] Documentation included

## Marketplace Features

### Theme Store
- Browse available themes
- Preview themes live
- Filter by color, style, popularity
- Rating and reviews
- Creator profiles

### Theme Submission
1. Developer uploads theme package
2. Automated validation runs
3. Manual review for quality
4. Approval and listing
5. Creator revenue share begins

### Monetization
- **Fixed Price**: One-time purchase
- **Subscription**: Monthly/yearly access
- **Free**: Community themes
- **Creator Share**: Revenue split (with caps and tiered reductions)

## Data Models

### Themes
- `Theme` - Theme metadata and configuration
- `ThemeAsset` - CSS, images, fonts
- `ThemeReview` - Approval review records
- `ThemePurchase` - Purchase transactions

### Analytics
- `ThemeUsage` - Installation analytics
- `ThemeRating` - User ratings and reviews
- `CreatorEarnings` - Revenue tracking

## Common Tasks

### Create New Theme
1. Define color palette in tokens
2. Set typography system
3. Style base components
4. Ensure accessibility compliance
5. Optimize CSS size
6. Create preview images
7. Package theme files
8. Submit for validation

### Validate Theme
1. Run automated checks (contrast, performance, accessibility)
2. Manual code review
3. Test on different browsers/devices
4. Verify responsive behavior
5. Check keyboard navigation
6. Approve/reject with feedback

### Manage Theme Store
1. Add new themes to marketplace
2. Update theme listings
3. Handle refunds/disputes
4. Calculate creator revenue shares
5. Generate analytics reports
6. Manage featured themes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuhao00) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
