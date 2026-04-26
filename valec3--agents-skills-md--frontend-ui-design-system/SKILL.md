---
name: frontend-ui-design-system
description: Standardized guidelines and patterns for Frontend Ui Design System. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Ui Design System

## When to use this skill
- Creating a design system
- Maintaining component library
- Ensuring design consistency

## Workflow
- [ ] Define design tokens (colors, spacing, typography)
- [ ] Create base components
- [ ] Document component usage
- [ ] Version design system
- [ ] Establish contribution guidelines

## Instructions

### Design Tokens
```typescript
// tokens/colors.ts
export const colors = {
  primary: {
    50: '#eff6ff',
    500: '#3b82f6',
    900: '#1e3a8a'
  },
  semantic: {
    success: '#10b981',
    error: '#ef4444',
    warning: '#f59e0b'
  }
};
```

### Component Structure
```
design-system/
├── tokens/
│   ├── colors.ts
│   ├── spacing.ts
│   └── typography.ts
├── components/
│   ├── Button/
│   ├── Input/
│   └── Card/
└── docs/
    └── [component].stories.tsx
```

## Resources
- Document all components
- Version with semantic versioning
- Use Storybook for documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
