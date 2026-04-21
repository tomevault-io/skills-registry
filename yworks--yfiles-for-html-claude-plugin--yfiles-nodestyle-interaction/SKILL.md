---
name: yfiles-nodestyle-interaction
description: name: yfiles-nodestyle-interaction Use when this capability is needed.
metadata:
  author: yworks
---
---
name: yfiles-nodestyle-interaction
description: This skill should be used when the user asks to "implement hit testing", "add custom click area", "implement isHit", "implement getOutline", "fix edge cropping", or mentions "hit testing", "isHit", "getOutline", "edge cropping", or "edge connection points" in a node style context.
---

# Node Style Interaction

Implement custom hit testing and edge cropping for accurate user interaction with non-rectangular node shapes.

## Before Implementing

Always query the yFiles MCP for current API:

```
yfiles:yfiles_get_symbol_details(name="NodeStyleBase")
yfiles:yfiles_list_members(name="NodeStyleBase")
yfiles:yfiles_search_by_description(query="hit testing")
yfiles:yfiles_get_symbol_details(name="GeneralPath")
```

## Quick Start

```typescript
export class InteractiveNodeStyle extends NodeStyleBase {
  protected isHit(
    context: IInputModeContext,
    location: Point,
    node: INode
  ): boolean {
    return node.layout.toRect().contains(location, context.hitTestRadius)
  }

  protected getOutline(node: INode): GeneralPath | null {
    const { x, y, width, height } = node.layout
    const path = new GeneralPath()
    path.appendRectangle(new Rect(x, y, width, height), false)
    return path
  }
}
```

## Core Concepts

- **isHit()**: Determines whether a point hits the node — implement for non-rectangular shapes
- **getOutline()**: Defines the node boundary for edge cropping — implement when edges should terminate at the custom shape edge
- **hitTestRadius**: Tolerance area around shape edges, provided by `context.hitTestRadius`
- **GeneralPath**: Arbitrary shape definition for outlines and hit regions

## When to Implement Each Method

- **isHit()**: Node shape is not rectangular (ellipses, polygons, custom paths)
- **getOutline()**: Edges should crop cleanly at the visible shape boundary

**Note**: When implementing yFiles interfaces, always use `BaseClass()` wrapper instead of TypeScript `implements`. The `@yfiles/eslint-plugin` rule `@yfiles/fix-implements-using-baseclass` enforces this pattern.

## Additional Resources

- **`references/examples.md`** — Hit testing for custom shapes, exclusion areas, circular/elliptical hit testing, complex outlines
- **`references/reference.md`** — Method signatures, GeneralPath API, geometric utilities

## Related Skills

- `/yfiles-nodestyle-basic` — Basic rendering
- `/yfiles-nodestyle-configure` — Configuration
- `/yfiles-nodestyle-advanced` — Performance and group nodes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
