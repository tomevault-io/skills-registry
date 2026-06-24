---
name: react-component-patterns
description: Modern React component architecture and composition patterns. Use when this capability is needed.
metadata:
  author: ngxtm
---

# React Component Patterns

## **Priority: P0 (CRITICAL)**

Standards for building scalable, maintainable React components.

## Implementation Guidelines

- **Function Components**: Only hooks. No classes.
- **Composition**: Use `children` prop. Avoid inheritance.
- **Props**: Explicit TS interfaces. Destructure in params.
- **Boolean Props**: Shorthand `<Cmp isVisible />` vs `isVisible={true}`.
- **Imports**: Group: `Built-in` -> `External` -> `Internal` -> `Styles`.
- **Error Boundaries**: Wrap app/features with `react-error-boundary`.
- **Size**: Small (< 250 lines). One component per file.
- **Naming**: `PascalCase` components. `use*` hooks.
- **Exports**: Named exports only.
- **Conditionals**: Ternary (`Cond ? <A/> : <B/>`) over `&&` for rendering consistency.
- **Hoisting**: Extract static JSX/Objects outside component to prevent recreation.

## Anti-Patterns

- **No Classes**: Use hooks.
- **No Prop Drilling**: Use Context/Zustand.
- **No Nested Definitions**: Define components at top level.
- **No Index Keys**: Use stable IDs.
- **No Inline Handlers**: Define before return.

## Code

```tsx
// Composition
export function Layout({ children, aside }: LayoutProps) {
  return (
    <div className='grid'>
      <aside>{aside}</aside>
      <main>{children}</main>
    </div>
  );
}

// Compound Component
export function Select({ children }: { children: ReactNode }) {
  return <select>{children}</select>;
}
Select.Option = ({ val, children }) => <option value={val}>{children}</option>;
```

## Reference & Examples

For advanced patterns (HOCs, Render Props, Compound Components):
See [references/REFERENCE.md](references/REFERENCE.md).

## Related Topics

hooks | state-management | performance

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngxtm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
