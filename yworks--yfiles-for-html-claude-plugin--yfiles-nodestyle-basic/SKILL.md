---
name: yfiles-nodestyle-basic
description: name: yfiles-nodestyle-basic Use when this capability is needed.
metadata:
  author: yworks
---
---
name: yfiles-nodestyle-basic
description: This skill should be used when the user asks to "create a custom node style", "implement NodeStyleBase", "create a custom node visualization", "implement createVisual", "implement updateVisual", "render nodes with SVG", or mentions "custom rendering", "TaggedSvgVisual", or "visual caching".
---

# Basic Custom Node Styles

Create custom node visualizations by extending `NodeStyleBase` and implementing SVG rendering with typed visual caching.

## Before Creating

Always query the yFiles MCP for current API:

```
yfiles:yfiles_get_symbol_details(name="NodeStyleBase")
yfiles:yfiles_get_symbol_details(name="TaggedSvgVisual")
yfiles:yfiles_list_members(name="NodeStyleBase")
yfiles:yfiles_search_documentation(query="custom node style")
```

## Quick Start (Minimal)

The simplest possible style — no caching, no `updateVisual`. Suitable for prototyping but not recommended for larger graphs.

```typescript
import { NodeStyleBase, SvgVisual, type IRenderContext, type INode } from '@yfiles/yfiles'

export class CustomNodeStyle extends NodeStyleBase {
  protected createVisual(context: IRenderContext, node: INode): SvgVisual {
    const rect = document.createElementNS('http://www.w3.org/2000/svg', 'rect')
    const { x, y, width, height } = node.layout
    rect.setAttribute('x', String(x))
    rect.setAttribute('y', String(y))
    rect.setAttribute('width', String(width))
    rect.setAttribute('height', String(height))
    rect.setAttribute('fill', '#0b7189')
    return new SvgVisual(rect)
  }
}
```

## Recommended Pattern (Typed Cache + updateVisual)

For production implementations, declare a `Cache` type and a named visual type using `TaggedSvgVisual`, then pass it as the type argument to `NodeStyleBase`. This enables typed access to `visual.tag` in `updateVisual` and avoids recreating DOM elements on every render frame.

```typescript
import {
  NodeStyleBase,
  SvgVisual,
  TaggedSvgVisual,
  type IRenderContext,
  type INode
} from '@yfiles/yfiles'

// 1. Declare the cache shape
type Cache = { width: number; height: number }

// 2. Declare the visual type — ties the SVG element type to the cache type
type CustomNodeStyleVisual = TaggedSvgVisual<SVGPathElement, Cache>

// 3. Pass the visual type as the type argument to NodeStyleBase
export class CustomNodeStyle extends NodeStyleBase<CustomNodeStyleVisual> {
  protected createVisual(
    context: IRenderContext,
    node: INode
  ): CustomNodeStyleVisual {
    const { x, y, width, height } = node.layout
    const pathElement = document.createElementNS(
      'http://www.w3.org/2000/svg',
      'path'
    )
    // Render at (0,0) and position via transform — required for efficient updates
    pathElement.setAttribute('d', createPathData(0, 0, width, height))
    SvgVisual.setTranslate(pathElement, x, y)
    pathElement.setAttribute('fill', '#0b7189')
    pathElement.setAttribute('stroke', '#042d37')
    // SvgVisual.from() creates a TaggedSvgVisual with the cache stored in .tag
    return SvgVisual.from(pathElement, { width, height })
  }

  protected updateVisual(
    context: IRenderContext,
    oldVisual: CustomNodeStyleVisual,
    node: INode
  ): CustomNodeStyleVisual {
    const { x, y, width, height } = node.layout
    const pathElement = oldVisual.svgElement
    const cache = oldVisual.tag  // fully typed as Cache

    // Only rebuild the path when size actually changed
    if (width !== cache.width || height !== cache.height) {
      pathElement.setAttribute('d', createPathData(0, 0, width, height))
      oldVisual.tag = { width, height }
    }

    // Always update position via transform
    SvgVisual.setTranslate(pathElement, x, y)
    return oldVisual
  }
}
```

## Core Concepts

- **createVisual()**: Creates the SVG visual (required)
- **updateVisual()**: Reuses the existing DOM element, updating only what changed (highly recommended for larger graphs)
- **TaggedSvgVisual\<TElement, TCache\>**: Typed wrapper that ties an SVG element type to a cache type via `.tag`
- **SvgVisual.from(element, cache)**: Convenience factory that creates a `TaggedSvgVisual` in one call
- **Render at origin + translate**: Render the shape at `(0,0)` and use `SvgVisual.setTranslate()` for position — only the transform needs updating when a node moves

## Implementation Steps

1. Declare a `Cache` type with the values needed to detect changes in `updateVisual`
2. Declare a `CustomNodeStyleVisual = TaggedSvgVisual<TElement, Cache>` type alias
3. Extend `NodeStyleBase<CustomNodeStyleVisual>`
4. In `createVisual()`: render at `(0,0)`, set translate, return `SvgVisual.from(element, cache)`
5. In `updateVisual()`: read `oldVisual.tag` (typed), update only changed attributes, always update translate

## Related Skills

To extend this foundation:
- `/yfiles-nodestyle-configure` — Make styles configurable and data-driven
- `/yfiles-nodestyle-interaction` — Add hit testing and edge cropping
- `/yfiles-nodestyle-advanced` — Viewport culling and group nodes

## Additional Resources

- **`references/examples.md`** — Complete examples: rectangle, custom path, group container, framework integration
- **`references/reference.md`** — NodeStyleBase/SvgVisual API, SVG patterns, performance tips, common pitfalls

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
