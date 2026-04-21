---
name: creating-3d-assets-blender
description: Creating 3D assets using Blender MCP for StickerNest. Use when the user asks to create 3D models, generate meshes, make Blender assets, create procedural geometry, export GLB/GLTF, or automate Blender workflows. Covers Blender Python API (bpy), primitives, materials, modifiers, scene management, and web-optimized export. Use when this capability is needed.
metadata:
  author: hkcm91
---

# Creating 3D Assets with Blender MCP

This skill enables creating, modifying, and exporting 3D assets directly from Blender using the Model Context Protocol (MCP). Assets can be used in StickerNest's spatial canvas, VR/AR experiences, and 3D widgets.

## Prerequisites

### Blender Setup
1. Install Blender 3.0+ from [blender.org](https://blender.org)
2. Download the `addon.py` from [ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp)
3. In Blender: Edit > Preferences > Add-ons > Install > Select `addon.py`
4. Enable "Interface: Blender MCP" addon

### Connect to Claude
1. Open Blender and press `N` to show the sidebar
2. Find the "BlenderMCP" tab
3. Click "Connect to Claude"
4. The MCP server will start on port 9876

## When to Use This Skill

This skill helps when you need to:
- Create 3D primitives (cubes, spheres, cylinders, etc.)
- Generate procedural geometry with Python
- Apply materials and colors to objects
- Export web-ready 3D models (GLB/GLTF)
- Automate repetitive 3D tasks
- Create assets for StickerNest widgets

## Core Concepts

### The bpy Module
Blender's Python API is exposed through the `bpy` module:
- `bpy.context` - Current state (active object, selected objects, scene)
- `bpy.data` - Access to all data (meshes, materials, objects)
- `bpy.ops` - Operators that perform actions (like menu commands)

### Object vs Mesh vs Material
- **Object**: Container with transform (location, rotation, scale)
- **Mesh**: Actual geometry data (vertices, edges, faces)
- **Material**: Surface appearance (color, roughness, metallic)

### Coordinate System
Blender uses right-handed Y-up coordinates. When exporting to Three.js/R3F (Y-up), models export correctly. For Unity (Y-up converted), use export options.

## Step-by-Step Guides

### Creating Basic Primitives

```python
import bpy

# Clear existing objects
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# Create a cube
bpy.ops.mesh.primitive_cube_add(
    size=2,
    location=(0, 0, 1)
)
cube = bpy.context.active_object
cube.name = "MyCube"

# Create a sphere
bpy.ops.mesh.primitive_uv_sphere_add(
    radius=1,
    segments=32,
    ring_count=16,
    location=(3, 0, 1)
)

# Create a cylinder
bpy.ops.mesh.primitive_cylinder_add(
    radius=0.5,
    depth=2,
    vertices=32,
    location=(-3, 0, 1)
)
```

### Applying Materials

```python
import bpy

# Get the active object
obj = bpy.context.active_object

# Create a new material
mat = bpy.data.materials.new(name="SN_PurpleAccent")
mat.use_nodes = True

# Configure the Principled BSDF shader
bsdf = mat.node_tree.nodes["Principled BSDF"]
# StickerNest purple accent: #8b5cf6 = RGB(139, 92, 246)
bsdf.inputs["Base Color"].default_value = (139/255, 92/255, 246/255, 1.0)
bsdf.inputs["Metallic"].default_value = 0.0
bsdf.inputs["Roughness"].default_value = 0.4

# Assign material to object
if obj.data.materials:
    obj.data.materials[0] = mat
else:
    obj.data.materials.append(mat)
```

### Adding Modifiers

```python
import bpy

obj = bpy.context.active_object

# Subdivision Surface (smooth geometry)
subsurf = obj.modifiers.new(name="Subdivision", type='SUBSURF')
subsurf.levels = 2
subsurf.render_levels = 3

# Bevel (rounded edges)
bevel = obj.modifiers.new(name="Bevel", type='BEVEL')
bevel.width = 0.02
bevel.segments = 2

# Solidify (add thickness to surfaces)
solidify = obj.modifiers.new(name="Solidify", type='SOLIDIFY')
solidify.thickness = 0.1

# Apply all modifiers
for mod in obj.modifiers:
    bpy.ops.object.modifier_apply(modifier=mod.name)
```

### Creating Procedural Geometry

```python
import bpy
import bmesh
from mathutils import Vector

# Create new mesh
mesh = bpy.data.meshes.new("ProceduralMesh")
obj = bpy.data.objects.new("ProceduralObject", mesh)
bpy.context.collection.objects.link(obj)

# Use bmesh for procedural creation
bm = bmesh.new()

# Create a custom shape (star)
import math
verts = []
for i in range(10):
    angle = math.radians(i * 36)
    radius = 1.0 if i % 2 == 0 else 0.5
    x = math.cos(angle) * radius
    y = math.sin(angle) * radius
    verts.append(bm.verts.new((x, y, 0)))

# Create face from vertices
bmesh.ops.contextual_create(bm, geom=verts)

# Extrude for thickness
bmesh.ops.extrude_face_region(bm, geom=bm.faces[:])
for v in bm.verts:
    if v.select:
        v.co.z += 0.2

bm.to_mesh(mesh)
bm.free()
```

### Exporting for Web (GLB)

```python
import bpy

# Select objects to export
bpy.ops.object.select_all(action='DESELECT')
for obj in bpy.context.scene.objects:
    if obj.type == 'MESH':
        obj.select_set(True)

# Export as GLB (binary GLTF)
bpy.ops.export_scene.gltf(
    filepath="C:/Users/Kimber/StickerNestV3-4/public/assets/3d/my-asset.glb",
    use_selection=True,
    export_format='GLB',
    export_apply=True,  # Apply modifiers
    export_texcoords=True,
    export_normals=True,
    export_materials='EXPORT',
    export_colors=True,
    export_cameras=False,
    export_lights=False,
    export_yup=True  # Convert to Y-up for Three.js
)
```

## Common Patterns

### Pattern: StickerNest-Themed Object

Create objects with StickerNest's color palette:

```python
import bpy

# StickerNest theme colors
SN_COLORS = {
    'bg_primary': (15/255, 15/255, 25/255, 1),      # #0f0f19
    'bg_secondary': (26/255, 26/255, 46/255, 1),    # #1a1a2e
    'accent_primary': (139/255, 92/255, 246/255, 1), # #8b5cf6
    'accent_secondary': (236/255, 72/255, 153/255, 1), # #ec4899
    'success': (34/255, 197/255, 94/255, 1),        # #22c55e
    'error': (239/255, 68/255, 68/255, 1),          # #ef4444
}

def create_sn_material(name, color_key, metallic=0.0, roughness=0.4):
    mat = bpy.data.materials.new(name=f"SN_{name}")
    mat.use_nodes = True
    bsdf = mat.node_tree.nodes["Principled BSDF"]
    bsdf.inputs["Base Color"].default_value = SN_COLORS[color_key]
    bsdf.inputs["Metallic"].default_value = metallic
    bsdf.inputs["Roughness"].default_value = roughness
    return mat

# Usage
accent_mat = create_sn_material("Accent", "accent_primary")
```

### Pattern: Widget Frame

Create a standard frame for 3D widgets:

```python
import bpy

def create_widget_frame(width=2, height=1.5, depth=0.1, corner_radius=0.1):
    # Create base plane
    bpy.ops.mesh.primitive_plane_add(size=1)
    frame = bpy.context.active_object
    frame.name = "WidgetFrame"

    # Scale to dimensions
    frame.scale = (width, height, 1)
    bpy.ops.object.transform_apply(scale=True)

    # Add solidify for depth
    solidify = frame.modifiers.new("Solidify", 'SOLIDIFY')
    solidify.thickness = depth

    # Add bevel for rounded corners
    bevel = frame.modifiers.new("Bevel", 'BEVEL')
    bevel.width = corner_radius
    bevel.segments = 4

    # Apply modifiers
    bpy.ops.object.modifier_apply(modifier="Solidify")
    bpy.ops.object.modifier_apply(modifier="Bevel")

    return frame

# Create a 4:3 widget frame
frame = create_widget_frame(width=2, height=1.5)
```

### Pattern: Batch Asset Generation

Generate multiple variations of an asset:

```python
import bpy
import math

def generate_icon_set(base_shape='sphere', variations=5):
    """Generate a set of icon variations"""

    assets = []

    for i in range(variations):
        # Clear selection
        bpy.ops.object.select_all(action='DESELECT')

        # Create base shape
        if base_shape == 'sphere':
            bpy.ops.mesh.primitive_uv_sphere_add(radius=0.5)
        elif base_shape == 'cube':
            bpy.ops.mesh.primitive_cube_add(size=1)
        elif base_shape == 'cylinder':
            bpy.ops.mesh.primitive_cylinder_add(radius=0.5, depth=0.2)

        obj = bpy.context.active_object
        obj.name = f"Icon_{base_shape}_{i}"

        # Apply unique transformation
        obj.location = (i * 1.5, 0, 0)
        obj.rotation_euler.z = math.radians(i * 15)

        # Apply unique material with hue shift
        hue = i / variations
        mat = bpy.data.materials.new(name=f"Icon_Mat_{i}")
        mat.use_nodes = True
        bsdf = mat.node_tree.nodes["Principled BSDF"]

        # HSV to RGB conversion (simplified)
        import colorsys
        r, g, b = colorsys.hsv_to_rgb(hue, 0.7, 0.9)
        bsdf.inputs["Base Color"].default_value = (r, g, b, 1)

        obj.data.materials.append(mat)
        assets.append(obj)

    return assets

# Generate 5 sphere icon variations
icons = generate_icon_set('sphere', 5)
```

## MCP Tool Reference

When Blender MCP is connected, use these tools:

### Scene Information
```
get_scene_info - Returns objects, materials, cameras in scene
get_object_info(name) - Get details about specific object
```

### Object Operations
```
create_object(type, name, location) - Create primitive
modify_object(name, location, rotation, scale) - Transform object
delete_object(name) - Remove object
```

### Materials
```
set_material(object_name, color, metallic, roughness) - Quick material
```

### Code Execution
```
execute_blender_code(code) - Run Python in Blender context
```

### External Assets
```
get_polyhaven_assets(type) - List Poly Haven HDRIs/textures/models
download_polyhaven_asset(name, type) - Download asset
search_sketchfab(query) - Search 3D models
import_sketchfab_model(url) - Import from Sketchfab
generate_hyper3d_model(prompt) - AI-generate 3D model
```

### Viewport
```
get_screenshot - Capture current viewport
```

## Export Checklist for StickerNest

Before exporting an asset for StickerNest:

- [ ] Triangle count under target (widgets: 10k, environments: 50k)
- [ ] All modifiers applied
- [ ] Materials use Principled BSDF (GLTF-compatible)
- [ ] UV coordinates present for textured objects
- [ ] Object origins centered appropriately
- [ ] Scale applied (Ctrl+A > Scale)
- [ ] No duplicate vertices (Mesh > Clean Up > Merge by Distance)
- [ ] Named appropriately (`sn-{category}-{name}`)
- [ ] Export with Y-up for Three.js compatibility

## Troubleshooting

### Issue: MCP not connecting
**Cause**: Blender addon not enabled or connection refused
**Fix**: Check addon is enabled, ensure port 9876 is free, restart Blender

### Issue: Export has wrong orientation
**Cause**: Blender uses Z-up, Three.js uses Y-up
**Fix**: Enable `export_yup=True` in GLTF export settings

### Issue: Materials look different in Three.js
**Cause**: Blender materials may use features not in GLTF spec
**Fix**: Stick to Principled BSDF with basic inputs only

### Issue: Model too large/heavy
**Cause**: High poly count or large textures
**Fix**: Use Decimate modifier, reduce texture resolution

## Reference Files

- **Agent definition**: `.claude/agents/blender-3d-creation.md`
- **MCP configuration**: `.claude/mcp.json`
- **3D asset folder**: `public/assets/3d/`
- **Widget models**: `src/widgets/builtin/*/assets/`

## Additional Resources

- [Blender Python API Docs](https://docs.blender.org/api/current/)
- [Blender MCP GitHub](https://github.com/ahujasid/blender-mcp)
- [GLTF Specification](https://github.com/KhronosGroup/glTF)
- [Poly Haven](https://polyhaven.com/) - Free HDRIs, textures, models

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hkcm91) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
