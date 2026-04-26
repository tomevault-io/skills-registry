---
name: frontend-ui-shadcn
description: Standardized guidelines and patterns for Frontend Ui Shadcn. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Ui Shadcn

## When to use this skill
- Using shadcn/ui components
- Customizing shadcn components
- Building with shadcn design system

## Workflow
- [ ] Install shadcn/ui
- [ ] Add components via CLI
- [ ] Customize in components/ui
- [ ] Apply design tokens
- [ ] Compose reusable patterns

## Instructions

### Installation
```bash
npx shadcn-ui@latest init
npx shadcn-ui@latest add button
```

### Usage
```typescript
import { Button } from '@/components/ui/button';

<Button variant="default">Click me</Button>
<Button variant="destructive">Delete</Button>
<Button variant="outline">Cancel</Button>
```

### Customization
Components are in `components/ui/` - modify directly.

## Resources
- Components are yours to own
- Built on Radix UI primitives
- Fully customizable with Tailwind

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
