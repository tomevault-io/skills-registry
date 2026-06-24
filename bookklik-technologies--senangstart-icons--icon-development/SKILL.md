---
name: icon-development
description: Guidelines for creating and optimizing SVG icons for SenangStart Icons Use when this capability is needed.
metadata:
  author: bookklik-technologies
---

# Icon Development Skill

This skill covers best practices for creating, optimizing, and adding icons to the library.

## Icon Requirements

### SVG Specifications

| Property | Value |
|----------|-------|
| ViewBox | `0 0 24 24` (standard) |
| Fill | `none` (stroke-based icons) |
| Stroke | `currentColor` |
| Stroke Width | `2` (default) |
| Stroke Linecap | `round` |
| Stroke Linejoin | `round` |

### Path Guidelines

1. **Use single path when possible** - Simpler paths are easier to render
2. **Optimize coordinates** - Round to 1-2 decimal places
3. **Center the icon** - Keep equal padding on all sides
4. **Design for stroke** - Icons should look good at varying stroke widths

## Creating an Icon

### Step 1: Design the SVG

Create in your preferred SVG editor (Figma, Illustrator, Inkscape):

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" 
     fill="none" stroke="currentColor" stroke-width="2"
     stroke-linecap="round" stroke-linejoin="round">
  <path d="M12 2L2 7l10 5 10-5-10-5z"/>
</svg>
```

### Step 2: Extract the Path

Copy just the `d` attribute value from the `<path>` element.

### Step 3: Add to icons.json

```json
{
  "name": "Layer Stack",
  "slug": "layer-stack",
  "src": "M12 2L2 7l10 5 10-5-10-5z",
  "tags": ["layer", "stack", "design", "graphics"]
}
```

### Step 4: Build and Verify

```bash
npm run build
npm run docs:dev
```

## Path Optimization

### Remove Unnecessary Precision

Before:
```
M12.000000 2.000000L2.000000 7.000000
```

After:
```
M12 2L2 7
```

### Combine Continuous Paths

Before:
```
M5 5L10 5 M10 5L15 5
```

After:
```
M5 5L15 5
```

### Use Relative Commands

Before:
```
M5 5L10 10L15 5
```

After:
```
M5 5l5 5l5-5
```

## Naming Conventions

### Icon Names (name field)
- Title case with spaces: `"Arrow Left"`, `"Shopping Cart"`
- Descriptive and concise

### Slugs (slug field)
- Lowercase with hyphens: `arrow-left`, `shopping-cart`
- Match the name but URL-safe
- Keep consistent with existing patterns

### Tags (tags array)
- Include broken-down name words
- Add related concepts
- Include action verbs if applicable
- Example: `["arrow", "left", "back", "previous", "navigate"]`

## Multi-Path Icons

For complex icons with multiple paths, combine them:

```json
{
  "name": "Complex Icon",
  "slug": "complex-icon",
  "src": "M5 5h14 M5 12h14 M5 19h14",
  "tags": ["menu", "hamburger", "bars"]
}
```

## Custom Properties

Override defaults when needed:

```json
{
  "name": "Filled Circle",
  "slug": "filled-circle",
  "src": "M12 12m-10 0a10 10 0 1 0 20 0a10 10 0 1 0-20 0",
  "fill": "currentColor",
  "stroke": "none",
  "tags": ["circle", "dot", "bullet"]
}
```

## Testing Icons

1. **Visual check** - Preview in browser via `index.html`
2. **Thickness test** - Verify icon looks good at various thicknesses
3. **Size test** - Check rendering at small (16px) and large (48px) sizes
4. **Color test** - Verify `currentColor` inheritance works

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bookklik-technologies) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
