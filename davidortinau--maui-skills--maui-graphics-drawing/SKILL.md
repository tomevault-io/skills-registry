---
name: maui-graphics-drawing
description: > Use when this capability is needed.
metadata:
  author: davidortinau
---

# .NET MAUI Graphics Drawing

## Common gotchas

| Issue | Fix |
|---|---|
| Nothing draws on screen | Ensure `Drawable` is set on `GraphicsView` and the control has non-zero `HeightRequest`/`WidthRequest` |
| State bleeds between shapes | Wrap isolated sections in `SaveState()` / `RestoreState()` pairs |
| Shadows stick to later draws | Call `canvas.SetShadow(SizeF.Zero, 0, null)` after drawing the shadowed element |
| Clipping never resets | Clipping is cumulative per frame — use `SaveState`/`RestoreState` around clip regions |
| UI freezes during drawing | Never do I/O, network, or heavy computation inside `Draw()` — it runs on the UI thread |

## Canvas state — always pair Save/Restore

⚠️ Unpaired `SaveState`/`RestoreState` causes state leaks across draw calls.

```csharp
// ✅ Correct — isolated state
canvas.SaveState();
canvas.StrokeColor = Colors.Red;
canvas.StrokeSize = 6;
canvas.DrawRectangle(10, 10, 80, 80);
canvas.RestoreState();
// Stroke reverts to previous values

// ❌ Wrong — state leaks to everything drawn after
canvas.StrokeColor = Colors.Red;
canvas.StrokeSize = 6;
canvas.DrawRectangle(10, 10, 80, 80);
// Every subsequent shape is now red with size 6
```

Saves/restores: stroke, fill, font, shadow, clip, and transforms. Nest calls for layered isolation.

## Triggering redraws

```csharp
// ✅ Correct — queue a redraw
graphicsView.Invalidate();

// ❌ Wrong — never call Draw() directly
myDrawable.Draw(canvas, rect);
```

- `Invalidate()` queues a redraw; the framework calls `IDrawable.Draw` on the next frame.
- ⚠️ Avoid calling `Invalidate()` in a tight loop — batch state changes, then invalidate once.

## Performance tips

- **Keep `Draw()` fast** — pre-compute paths and data outside the draw method; `Draw()` is called on every frame.
- **Reuse `PathF` objects** — create them once, store as fields, draw repeatedly.
- **Use `MeasureFirstItem`-style thinking** — if drawing many identical items, calculate dimensions once.
- **Minimize allocations** — avoid `new PathF()` inside `Draw()` when the path doesn't change.

```csharp
// ✅ Pre-computed path (field)
private readonly PathF _starPath = BuildStarPath();

public void Draw(ICanvas canvas, RectF dirtyRect)
{
    canvas.FillPath(_starPath);
}

// ❌ Allocating every frame
public void Draw(ICanvas canvas, RectF dirtyRect)
{
    var path = new PathF();
    // ...build path every frame...
    canvas.FillPath(path);
}
```

## Shadows and clipping — sticky state pitfalls

Shadows apply to **all subsequent draws** until explicitly removed. Clips accumulate and can only be undone with `RestoreState()`:

```csharp
// ✅ Shadow: remove after use
canvas.SetShadow(new SizeF(5, 5), 4, Colors.Gray);
canvas.FillRectangle(20, 20, 100, 60);
canvas.SetShadow(SizeF.Zero, 0, null); // ← must remove

// ✅ Clip: isolate with SaveState/RestoreState
canvas.SaveState();
canvas.ClipRectangle(20, 20, 100, 100);
canvas.FillRectangle(0, 0, 200, 200); // clipped
canvas.RestoreState();                  // clip removed

// ❌ Clip persists — everything after is also clipped
canvas.ClipRectangle(20, 20, 100, 100);
canvas.FillEllipse(150, 150, 50, 50); // unintentionally clipped!
```

## Set properties BEFORE draw calls

```csharp
// ✅ Properties then draw
canvas.StrokeColor = Colors.Blue;
canvas.DrawRectangle(10, 10, 100, 50);

// ❌ Setting after draw has no effect on previous shape
canvas.DrawRectangle(10, 10, 100, 50);
canvas.StrokeColor = Colors.Blue;
```

## Decision framework

| Need | Approach |
|---|---|
| Simple shapes / static graphics | Single `IDrawable`, draw in `Draw()` |
| Animated graphics | Update state externally, call `Invalidate()` from timer/animation |
| Complex layered scene | Multiple `SaveState`/`RestoreState` blocks, or separate drawables |
| Hit testing on drawn elements | Track shape bounds manually — `GraphicsView` has no built-in hit test on drawn content |
| Platform-specific rendering | Use handlers/platform code; `Microsoft.Maui.Graphics` is cross-platform only |

## Quick checklist

- [ ] `GraphicsView` has `Drawable` set and non-zero size
- [ ] `Draw()` is fast — no I/O, no heavy allocations
- [ ] Every `SaveState()` has a matching `RestoreState()`
- [ ] Shadows removed with `SetShadow(SizeF.Zero, 0, null)` after use
- [ ] Clips wrapped in `SaveState`/`RestoreState` blocks
- [ ] Properties set **before** the draw call they apply to
- [ ] `Invalidate()` used instead of calling `Draw()` directly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidortinau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
