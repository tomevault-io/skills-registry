---
name: get-material-nodes
description: > Use when this capability is needed.
metadata:
  author: IvanYangYangXi
---

# Get Material Nodes — 材质节点图查询

从材质属性入口（BaseColor、Roughness、Normal 等 16 个属性）通过 BFS 遍历所有连接的 MaterialExpression 节点。

## Tool

`get_material_nodes(material_path, [max_depth], [max_nodes])`

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `material_path` | string | required | Content browser path, e.g. `/Game/Materials/M_Example` |
| `max_depth` | int | 20 | BFS max depth (upper limit: 50) |
| `max_nodes` | int | 200 | Max collected nodes (upper limit: 500) |

### Returns

Each node includes:
- **class** — e.g. `MaterialExpressionVectorParameter`
- **pos** — editor graph position `[x, y]`
- **inputs** — input connections (source node, output pin name)
- **props** — key properties (parameter_name, default_value, texture, etc.)

Top-level response includes `property_connections` mapping material properties to their directly connected nodes.

## Safety

- Per-class property reading (no blind `get_editor_property`)
- Depth + node count dual limits prevent hangs on complex materials
- Returns `truncated: true` when limits are hit
- 74-node sky material benchmarked at < 5ms

## Limitations

- Only `Material` assets — not `MaterialInstance` (use `get_material_parameters`)
- Only connected nodes reachable via BFS — floating nodes are inaccessible through Python API
- `total_expressions` vs `collected` difference = floating node count

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/IvanYangYangXi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
