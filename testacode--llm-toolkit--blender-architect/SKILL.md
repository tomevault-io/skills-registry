---
name: blender-architect
description: | Use when this capability is needed.
metadata:
  author: testacode
---

# Blender Architect

Professional architectural modeling workflow for Blender via MCP.

## Prerequisites

- Blender with MCP addon connected
- Verify connection: `mcp__blender__get_scene_info`

## Core Workflow

### Phase 1: Scene Setup

```python
import bpy

# Clear scene
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# Create ground at Z=0
bpy.ops.mesh.primitive_plane_add(size=1, location=(width/2, length/2, 0))
ground = bpy.context.active_object
ground.name = "Ground"
ground.scale = (width, length, 1)
bpy.ops.object.transform_apply(scale=True)
```

### Phase 2: Wall Creation (Solidify Method)

**Key technique**: Create 2D outline → Solidify for thickness → Extrude for height.

```python
import bmesh

def create_walls_from_outline(vertices, thickness=0.20, height=2.95, name="Walls"):
    """
    vertices: list of (x, y) tuples defining wall outline
    thickness: wall thickness in meters
    height: wall height in meters
    """
    # Create mesh from vertices
    mesh = bpy.data.meshes.new(f"{name}_mesh")
    obj = bpy.data.objects.new(name, mesh)
    bpy.context.collection.objects.link(obj)

    bm = bmesh.new()
    verts = [bm.verts.new((v[0], v[1], 0)) for v in vertices]

    # Create edges (closed loop)
    for i in range(len(verts)):
        bm.edges.new((verts[i], verts[(i+1) % len(verts)]))

    # Create face from edges
    bm.faces.new(verts)
    bm.to_mesh(mesh)
    bm.free()

    # Solidify for wall thickness
    solidify = obj.modifiers.new("Solidify", 'SOLIDIFY')
    solidify.thickness = thickness
    solidify.offset = -1
    solidify.use_even_offset = True

    bpy.context.view_layer.objects.active = obj
    bpy.ops.object.modifier_apply(modifier="Solidify")

    # Extrude for height
    bpy.ops.object.mode_set(mode='EDIT')
    bpy.ops.mesh.select_all(action='SELECT')
    bpy.ops.mesh.extrude_region_move(
        TRANSFORM_OT_translate={"value": (0, 0, height)}
    )
    bpy.ops.object.mode_set(mode='OBJECT')

    return obj
```

### Phase 3: Openings (Boolean Method)

```python
def create_opening(wall_obj, location, size, name="Opening"):
    """
    Cut door/window opening using boolean difference.
    location: (x, y, z) center of opening
    size: (width, depth, height) of opening
    """
    # Create cutter
    bpy.ops.mesh.primitive_cube_add(size=1, location=location)
    cutter = bpy.context.active_object
    cutter.name = f"Cutter_{name}"
    cutter.scale = (size[0]/2, size[1]/2, size[2]/2)
    bpy.ops.object.transform_apply(scale=True)

    # Boolean difference
    bool_mod = wall_obj.modifiers.new(f"Bool_{name}", 'BOOLEAN')
    bool_mod.operation = 'DIFFERENCE'
    bool_mod.object = cutter

    bpy.context.view_layer.objects.active = wall_obj
    bpy.ops.object.modifier_apply(modifier=f"Bool_{name}")

    # Remove cutter
    bpy.data.objects.remove(cutter)
```

### Phase 4: Roof

```python
def create_sloped_roof(vertices, base_height, slope_percent=10, overhang=0.3):
    """
    Create inclined roof slab.
    slope_percent: 5-15% typical for residential
    """
    import math

    bpy.ops.mesh.primitive_plane_add(size=1)
    roof = bpy.context.active_object
    roof.name = "Roof"

    # Position and scale
    min_x = min(v[0] for v in vertices) - overhang
    max_x = max(v[0] for v in vertices) + overhang
    min_y = min(v[1] for v in vertices) - overhang
    max_y = max(v[1] for v in vertices) + overhang

    width = max_x - min_x
    length = max_y - min_y

    roof.location = ((min_x + max_x)/2, (min_y + max_y)/2, base_height)
    roof.scale = (width, length, 1)
    bpy.ops.object.transform_apply(scale=True)

    # Apply slope
    slope_rad = math.atan(slope_percent / 100)
    roof.rotation_euler = (slope_rad, 0, 0)

    # Add thickness
    solidify = roof.modifiers.new("Thickness", 'SOLIDIFY')
    solidify.thickness = 0.15
    bpy.ops.object.modifier_apply(modifier="Thickness")

    return roof
```

### Phase 5: Materials

```python
def create_arch_material(name, color, roughness=0.8, metallic=0.0):
    mat = bpy.data.materials.new(name=name)
    mat.use_nodes = True
    bsdf = mat.node_tree.nodes["Principled BSDF"]
    bsdf.inputs["Base Color"].default_value = (*color, 1)
    bsdf.inputs["Roughness"].default_value = roughness
    bsdf.inputs["Metallic"].default_value = metallic
    return mat

# Standard materials
MATERIALS = {
    "wall_exterior": ((0.75, 0.75, 0.73), 0.9, 0.0),
    "wall_interior": ((0.95, 0.95, 0.93), 0.95, 0.0),
    "concrete": ((0.5, 0.5, 0.5), 0.8, 0.0),
    "metal_black": ((0.05, 0.05, 0.05), 0.2, 0.9),
    "roof_metal": ((0.35, 0.35, 0.38), 0.4, 0.7),
    "glass": ((0.8, 0.85, 0.9), 0.0, 0.0),
}
```

## Complete House Example

See [references/house_example.md](references/house_example.md) for full implementation.

## Standard Dimensions

| Element | Dimension |
|---------|-----------|
| Wall height | 2.95m (to ceiling) |
| Exterior wall | 0.20m thick |
| Interior wall | 0.15m thick |
| Door height | 2.10m |
| Door width | 0.80-0.90m |
| Window sill | 0.90m from floor |
| Window height | 1.20m |

## Common Mistakes to Avoid

1. **Floating objects** - Always start from Z=0
2. **Separate cubes for walls** - Use Solidify on outline instead
3. **Overlapping windows** - Use Boolean to cut, don't place on top
4. **Missing scale apply** - Always `bpy.ops.object.transform_apply(scale=True)`
5. **Wrong normals** - Recalculate after boolean operations

## Troubleshooting

- **Boolean fails**: Check mesh is manifold, no self-intersections
- **Solidify uneven**: Enable "Even Thickness" option
- **Objects not visible**: Check collection visibility and Z position
- **MCP unavailable**: Ensure Blender MCP addon is running and reconnect

See `scripts/arch_utils.py` for reusable utility functions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/testacode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
