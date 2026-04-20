---
name: client-portal-frontend-components
description: Rules for building frontend/user interface for use in client-portal Use when this capability is needed.
metadata:
  author: codyktam
---

## Frontend: Components

### Use shared components
Import reusable components from `ui-components` using the `@components/*` alias:

```ts
import Button from '@components/atoms/Button';
import Input from '@components/atoms/Input';
import Form from '@components/atoms/Form';
```

Always check `ui-components/src/` for existing components before creating new ones.

### Design tokens (Pollen)
Use Tailwind classes with Pollen design tokens for consistent styling.

**Semantic colors (preferred):**
- `primary`, `secondary`, `accent` - brand actions
- `success`, `error`, `warning`, `info` - feedback states
- `background`, `foreground`, `muted`, `border` - neutrals

**Typography:**
- `font-title` - headings
- `font-body` - body text

```tsx
// Correct - semantic tokens
<button className="bg-primary text-white">Submit</button>
<span className="text-error">Required field</span>
<div className="bg-muted text-foreground">Card</div>

// Avoid - legacy brand colors
<button className="bg-snout-orange">Submit</button>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codyktam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
