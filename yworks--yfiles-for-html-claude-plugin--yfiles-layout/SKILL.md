---
name: yfiles-layout
description: description: This skill should be used when the user asks to "apply a layout", "arrange nodes", "organize the graph", "apply hierarchical layout", "use organic layout", "use tree layout", or mentions layout algorithms like "HierarchicalLayout", "OrganicLayout", "TreeLayout", "CircularLayout", "OrthogonalLayout", or "RadialLayout". Use when this capability is needed.
metadata:
  author: yworks
---
---
name: yfiles-layout
description: This skill should be used when the user asks to "apply a layout", "arrange nodes", "organize the graph", "apply hierarchical layout", "use organic layout", "use tree layout", or mentions layout algorithms like "HierarchicalLayout", "OrganicLayout", "TreeLayout", "CircularLayout", "OrthogonalLayout", or "RadialLayout".
---

# Apply Automatic Layouts

Execute layout algorithms to automatically arrange graph elements.

## Before Applying

Always query the yFiles MCP for current API:

```
yfiles:yfiles_search_by_description(query="layout algorithm")
yfiles:yfiles_get_symbol_details(name="HierarchicalLayout")
yfiles:yfiles_get_symbol_details(name="LayoutExecutor")
```

## Quick Start

```typescript
import { HierarchicalLayout, LayoutExecutor } from '@yfiles/yfiles'

await new LayoutExecutor({
  graphComponent,
  layout: new HierarchicalLayout(),
  animationDuration: '0.5s'
}).start()
```

## Choosing a Layout

| Graph type | Algorithm |
|---|---|
| Tree (no cycles) | `TreeLayout` |
| Directed / hierarchical | `HierarchicalLayout` |
| Undirected network | `OrganicLayout` |
| Center focus / star | `RadialLayout` |
| Orthogonal routing | `OrthogonalLayout` |
| Circular / cyclic | `CircularLayout` |

## LayoutExecutor Options

```typescript
new LayoutExecutor({
  graphComponent,          // Required
  layout,                  // Required: ILayoutAlgorithm
  layoutData,              // Optional: layout-specific constraints/hints
  animationDuration: '1s',
  animateViewport: true,
  easedAnimation: true
})
```

## Additional Resources

- **`references/algorithms.md`** — Per-algorithm configuration: HierarchicalLayout, OrganicLayout, TreeLayout, CircularLayout, OrthogonalLayout, RadialLayout
- **`references/configuration.md`** — LayoutData, sequential layouts, partial layouts, incremental layouts, fixed nodes
- **`references/examples.md`** — Complete working code examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
