---
name: yfiles-nodestyle-advanced
description: name: yfiles-nodestyle-advanced Use when this capability is needed.
metadata:
  author: yworks
---
---
name: yfiles-nodestyle-advanced
description: This skill should be used when the user asks to "implement viewport culling", "implement isVisible", "implement getBounds", "create a group node style", "optimize rendering performance", or mentions "isVisible", "getBounds", "group node style", "folder tab", "viewport culling", or "rendering outside layout bounds".
---

# Advanced Node Style Features

Implement viewport culling, custom rendering bounds, and group node styles.

## Before Implementing

Always query the yFiles MCP for current API:

```
yfiles:yfiles_get_symbol_details(name="NodeStyleBase")
yfiles:yfiles_list_members(name="NodeStyleBase")
yfiles:yfiles_search_documentation(query="group node style")
yfiles:yfiles_get_symbol_details(name="INode")
```

## Quick Start

```typescript
export class AdvancedNodeStyle extends NodeStyleBase {
  protected isVisible(
    context: ICanvasContext,
    rectangle: Rect,
    node: INode
  ): boolean {
    // Only render if node intersects viewport
    return node.layout.toRect().intersects(rectangle)
  }

  protected getBounds(context: ICanvasContext, node: INode): Rect {
    // Return actual rendering bounds (including badges, shadows, etc.)
    return node.layout.toRect().getEnlarged(10)
  }
}
```

## Core Concepts

- **isVisible()**: Skips rendering when the node is outside the viewport — implement when the node renders outside its layout bounds (e.g. shadows, glows)
- **getBounds()**: Returns the actual rendered bounding box — implement when visual elements extend beyond `node.layout` (badges, decorators)
- **Group node styles**: Style class for container/parent nodes in hierarchical graphs
- **Folding**: Collapsible group nodes

## When to Implement Each Method

- **isVisible()**: Node renders content beyond its layout rectangle
- **getBounds()**: Badges, drop shadows, or other decorators extend outside the layout rect
- **Group styles**: Hierarchical graph with parent/child node relationships

**Note**: When implementing yFiles interfaces, always use `BaseClass()` wrapper instead of TypeScript `implements`. The `@yfiles/eslint-plugin` rule `@yfiles/fix-implements-using-baseclass` enforces this pattern.

## Additional Resources

- **`references/examples.md`** — Viewport culling, custom bounds with badges, group node styles, folder tab rendering
- **`references/reference.md`** — `isVisible()` strategies, `getBounds()` patterns, group node API, performance considerations

## Related Skills

- `/yfiles-nodestyle-basic` — Start here for rendering fundamentals
- `/yfiles-nodestyle-configure` — Data-driven configuration
- `/yfiles-nodestyle-interaction` — Hit testing and edge cropping

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
