---
name: openscad-3d-modeling
description: OpenSCAD-based 3D modeling for programmatic generation of solid CAD models suitable for 3D printing. Use when generating mechanical parts, parametric designs, functional prototypes, geometric shapes, or any 3D printable models. Handles Constructive Solid Geometry (CSG) operations, primitive shapes (cube, sphere, cylinder, polyhedron), transformations (translate, rotate, scale), and export to STL/3MF formats. Ideal for AI-driven model generation where scripts are created and rendered headless. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# OpenSCAD 3D Modeling

## Overview

OpenSCAD is a programmer's solid 3D CAD modeller that uses scripts to generate 3D models. Unlike interactive 3D tools, OpenSCAD models are described declaratively in code, making them ideal for AI-driven generation. Models are built from primitives (cube, sphere, cylinder) combined with boolean operations (union, difference, intersection) and transformed (translate, rotate, scale).

### Key Concepts

**Constructive Solid Geometry (CSG)**: Combine simple shapes into complex models using boolean operations
- `union()` - Combine multiple shapes into one
- `difference()` - Subtract shapes from each other
- `intersection()` - Keep only overlapping regions

**Primitives**: Basic building blocks
- `cube(size=[x,y,z])` - Rectangular box
- `sphere(r=radius)` - Sphere
- `cylinder(h=height, r=radius)` - Cylinder
- `polyhedron(points, faces)` - Arbitrary shape

**Transformations**: Position and orient shapes
- `translate([x,y,z])` - Move shape
- `rotate([x,y,z])` - Rotate around axes
- `scale([x,y,z])` - Resize shape
- `resize([x,y,z])` - Resize to specific dimensions

### Render Pipeline

```
OpenSCAD Script (.scad)
         ↓
    Preview (Fast, GPU, approximate)
         ↓
    Full Render (Slow, CPU, exact)
         ↓
    Export (STL, 3MF, OFF, DXF)
```

## Workflow Decision Tree

```
User Request
    ↓
    Is this for 3D printing?
    ├── YES → Generate OpenSCAD script
    │         ↓
    │     Validate script syntax
    │         ↓
    │     Render preview (openscad -o preview.png input.scad)
    │         ↓
    │     Full render for export (openscad -o output.stl input.scad)
    │         ↓
    │     Return .stl + .scad + preview image
    │
    └── NO → May need Three.js (different skill)
             or clarify user intent
```

## Step 1: Generate OpenSCAD Script

### Syntax Basics

```openscad
// Resolution control for 3D printing quality
$fa = 1;  // Minimum angle (degrees)
$fs = 0.4;  // Minimum size (mm)

// Simple box
cube([20, 20, 10]);

// Sphere
sphere(r=10);

// Cylinder
cylinder(h=20, r=5);
```

### Combine Shapes with CSG

```openscad
// Union - combine shapes
union() {
    cube([20, 10, 10]);
    translate([20, 0, 0])
        cube([10, 10, 10]);
}

// Difference - subtract shapes
difference() {
    cube([20, 20, 10]);
    translate([5, 5, -1])
        cylinder(h=12, r=3);
}

// Intersection - keep overlap
intersection() {
    cube([20, 20, 20]);
    sphere(r=15);
}
```

### Transformations

```openscad
// Translate
translate([10, 10, 0])
    cube([10, 10, 10]);

// Rotate
rotate([90, 0, 0])
    cylinder(h=20, r=5);

// Scale
scale([2, 1, 1])
    sphere(r=10);

// Chain transformations
translate([10, 0, 0])
    rotate([0, 90, 0])
        cylinder(h=10, r=5);
```

### Modules for Reusability

```openscad
module gear(teeth=8, radius=20, thickness=5) {
    union() {
        cylinder(h=thickness, r=radius);
        for (i = [0:teeth-1]) {
            rotate([0, 0, i * 360/teeth])
                translate([radius, 0, 0])
                    cube([5, 3, thickness], center=true);
        }
    }
}

// Use module
gear(teeth=12, radius=15, thickness=3);
```

## Step 2: Validate Script

### Common Validation Checks

1. **Syntax errors**: Missing semicolons, unmatched brackets
2. **Valid geometry**: Manifold edges, no self-intersections
3. **Overlap tolerance**: Use 0.001mm overlap between touching surfaces

### Validation Script

Use `scripts/validate_openscad.py` to check:

```bash
python3 scripts/validate_openscad.py model.scad
```

Returns:
- ✅ Valid: Script compiles successfully
- ❌ Error: Syntax error message

## Step 3: Render Model

### Quick Preview (for iteration)

```bash
openscad -o preview.png input.scad
```

- Fast GPU rendering
- Good for checking geometry
- Not export-quality

### Full Render (for export)

```bash
openscad -o output.stl input.scad
```

- Slow CPU rendering with CGAL
- Produces exact geometry
- Ready for 3D printing

### Export Formats

| Format | Use Case | Command |
|--------|----------|---------|
| STL | 3D printing (most common) | `openscad -o model.stl input.scad` |
| 3MF | Modern 3D printing (metadata + color) | `openscad -o model.3mf input.scad` |
| OFF | Simple ASCII format | `openscad -o model.off input.scad` |
| DXF | 2D drawings (laser cutter) | `openscad -o model.dxf input.scad` |
| PNG | Preview image | `openscad -o model.png input.scad` |

## Best Practices

### 1. Resolution Control

Always set resolution variables at the top:

```openscad
$fa = 1;   // Minimum angle: 1 degree
$fs = 0.4; // Minimum size: 0.4mm
```

- `$fa`: Minimum angle for circle segments (lower = smoother)
- `$fs`: Minimum segment size (lower = finer detail)
- Default values may create faceted curves

### 2. Overlap Tolerance

When shapes touch, ensure small overlap (0.001mm):

```openscad
difference() {
    cube([20, 20, 10]);
    translate([5, 5, -0.001])  // Small overlap
        cylinder(h=10.002, r=3);
}
```

### 3. Use Modules

Encapsulate complex geometry in modules:

```openscad
module bolt_hole(length, diameter) {
    cylinder(h=length, r=diameter/2);
}

// Use multiple times
difference() {
    plate();
    translate([10, 10, 0]) bolt_hole(10, 5);
    translate([20, 10, 0]) bolt_hole(10, 5);
}
```

### 4. Parameterize Design

Make designs configurable:

```openscad
module box(width=50, depth=50, height=20, wall_thick=2) {
    difference() {
        cube([width, depth, height]);
        translate([wall_thick, wall_thick, wall_thick])
            cube([width-2*wall_thick,
                  depth-2*wall_thick,
                  height]);
    }
}

// Use with parameters
box(width=60, depth=40, height=15, wall_thick=3);
```

## Common Patterns

### Hollow Box

```openscad
module hollow_box(width, depth, height, wall_thick) {
    difference() {
        cube([width, depth, height]);
        translate([wall_thick, wall_thick, wall_thick])
            cube([width-2*wall_thick,
                  depth-2*wall_thick,
                  height-wall_thick]);
    }
}
```

### Fillet (Rounded Edge)

```openscad
module fillet(radius, length) {
    difference() {
        cube([radius, radius, length]);
        translate([radius, radius, -1])
            cylinder(h=length+2, r=radius);
    }
}
```

### Bracket

```openscad
module bracket(width=40, height=30, thickness=5, hole_diameter=8) {
    difference() {
        // L-shape
        union() {
            cube([width, thickness, thickness]);
            cube([thickness, height, thickness]);
        }
        // Holes
        translate([width/2, -1, thickness/2])
            rotate([-90, 0, 0])
                cylinder(h=thickness+2, r=hole_diameter/2);
        translate([-1, height/2, thickness/2])
            rotate([0, 90, 0])
                cylinder(h=thickness+2, r=hole_diameter/2);
    }
}
```

### Gear

```openscad
module gear(teeth=8, radius=20, thickness=5, hole_diameter=5) {
    difference() {
        union() {
            // Main body
            cylinder(h=thickness, r=radius);
            // Teeth
            for (i = [0:teeth-1]) {
                rotate([0, 0, i * 360/teeth])
                    translate([radius, 0, 0])
                        cube([radius/2, radius/teeth*2, thickness], center=true);
            }
        }
        // Center hole
        cylinder(h=thickness+1, r=hole_diameter/2, center=true);
    }
}
```

## Resources

### scripts/

- **validate_openscad.py**: Validate OpenSCAD scripts for syntax errors
- Use when checking generated scripts before rendering

### references/

- **primitives.md**: Complete list of OpenSCAD primitives with examples
- **transformations.md**: Transformation operations and patterns
- **modules.md**: Common reusable modules and patterns
- **export_formats.md**: Export format specifications and use cases

### assets/

- **templates/simple_box.scad**: Basic box template
- **templates/mechanical_part.scad**: Mechanical part template
- **templates/parametric_design.scad**: Parametric design template
- **templates/assembly.scad**: Assembly template

## Resources

See [references/primitives.md](references/primitives.md) for complete primitive reference.
See [references/modules.md](references/modules.md) for reusable module patterns.
See [assets/templates/](assets/templates/) for starting templates.

Use `scripts/validate_openscad.py` to validate scripts before rendering.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
