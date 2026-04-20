---
name: create-component
description: Create a new React component following project conventions Use when this capability is needed.
metadata:
  author: nismit
---

# Create Component: $ARGUMENTS

## Structure

```
src/components/[ComponentName]/
├── index.tsx        # Main component
├── styles.ts        # Tailwind styles (optional)
└── [ComponentName].test.tsx  # Tests
```

## Template

```tsx
// src/components/[ComponentName]/index.tsx
type Props = {
  // Define props here
};

export const ComponentName = ({ ...props }: Props) => {
  return (
    <div className="...">
      {/* Component content */}
    </div>
  );
};
```

## Checklist

1. Create component file with proper TypeScript types
2. Use Tailwind CSS for styling
3. Add test file with basic render test
4. Export from component index

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nismit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
