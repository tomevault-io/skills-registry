---
name: yfiles-interactivity
description: name: yfiles-interactivity Use when this capability is needed.
metadata:
  author: yworks
---
---
name: yfiles-interactivity
description: This skill should be used when the user asks to "add tooltips", "add a context menu", "implement graph search", "add an overview", "handle click events", "handle selection changes", or mentions "tooltip", "context menu", "search", "GraphOverviewComponent", "click handler", "selection changed", "hover effects", or "interactive features".
---

# User Interactivity

Add interactive features — tooltips, context menus, click handlers, graph search, and overview navigation — to yFiles applications.

## Before Implementing

Always query the yFiles MCP for current API:

```
yfiles:yfiles_get_symbol_details(name="GraphEditorInputMode")
yfiles:yfiles_list_members(name="GraphEditorInputMode", includeInherited=true)
yfiles:yfiles_get_symbol_details(name="GraphOverviewComponent")
yfiles:yfiles_search_documentation(query="tooltips context menu")
```

## Quick Start

```typescript
// Tooltips
inputMode.addEventListener('query-item-tool-tip', (evt) => {
  evt.toolTip = createTooltipContent(evt.item)
  evt.handled = true
})

// Context menu
inputMode.addEventListener('populate-item-context-menu', (evt) => {
  evt.contextMenu = [
    { label: 'Delete', action: () => inputMode.deleteSelection() }
  ]
})

// Overview
const overview = new GraphOverviewComponent('overviewDiv', graphComponent)

// Search with highlighting
graphComponent.highlights.clear()
graph.nodes.forEach(node => {
  if (matches(node, searchText)) {
    graphComponent.highlights.add(node)
  }
})
```

## Core Concepts

- **Tooltips**: `query-item-tool-tip` event on `inputMode`, set `evt.toolTip` and `evt.handled = true`
- **Context menus**: `populate-item-context-menu` event, assign `evt.contextMenu` array of `{ label, action }` items
- **Click events**: `item-clicked`, `item-double-clicked`, `item-right-clicked` on `inputMode`
- **Graph search**: Highlights manager + custom matching logic; use `graphComponent.highlights`
- **Overview**: `GraphOverviewComponent` for minimap navigation
- **Selection events**: `selection.item-added`, `selection.item-removed`, `current-item-changed`

**Note**: Always use `addEventListener` (yFiles 3.0) instead of `addListener` (yFiles 2.6). The `@yfiles/eslint-plugin` rule `@yfiles/replace-legacy-event-listeners` enforces this and auto-fixes legacy patterns.

## Additional Resources

- **`references/examples.md`** — Tooltip with HTML content, conditional context menu items, search with highlighting, overview integration, click handlers, selection listeners
- **`references/reference.md`** — Event signatures, `ToolTipInputMode` options, context menu item format, `GraphOverviewComponent` API, highlight manager, selection API

## Related Skills

- `/yfiles-init` — Initial setup
- `/yfiles-graphbuilder` — Build graphs from data
- `/yfiles-nodestyle-basic` — Custom visuals for highlighting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
