---
name: rendering-absolute-coords
description: Renders MewUI custom elements using absolute Bounds coordinates. Use when alignment appears ignored or element renders at top-left. Use when this capability is needed.
metadata:
  author: christian289
---

# MewUI Absolute Coordinates

MewUI does NOT apply coordinate transforms. Each element must render at its absolute `Bounds` position.

## Problem

```csharp
// ❌ WRONG - Always draws at top-left
public override void Render(IGraphicsContext context)
{
    context.FillRectangle(new Rect(0, 0, Width, Height), color);
}
```

## Solution

```csharp
// ✅ CORRECT - Use Bounds.X and Bounds.Y
public override void Render(IGraphicsContext context)
{
    double ox = Bounds.X;
    double oy = Bounds.Y;

    context.FillRectangle(new Rect(ox, oy, Width, Height), color);
}
```

## Checklist

- Use `Bounds.X` and `Bounds.Y` as offset in Render
- Apply offset to ALL drawing operations
- Override `MeasureContent` to return `new Size(Width, Height)`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/christian289) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
