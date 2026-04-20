---
name: video-common
description: Shared Remotion video utilities and patterns. Referenced by other video skills - not invoked directly. Use when this capability is needed.
metadata:
  author: aviz85
---

# Remotion Common Utilities

Shared patterns and references for all video skills in this project.

## Project Structure

```
remotion-assistant/
├── src/
│   ├── index.tsx          # Entry point
│   ├── Root.tsx           # Composition registry
│   ├── compositions/      # Video compositions
│   └── components/        # Reusable components
├── public/                # Static assets
│   ├── fonts/
│   ├── images/
│   └── audio/
└── out/                   # Rendered output
```

## Key APIs

- `useCurrentFrame()` - Current frame number
- `useVideoConfig()` - { width, height, fps, durationInFrames }
- `interpolate(frame, [in], [out])` - Map values
- `spring({ frame, fps, config })` - Physics animations

## Adding New Compositions

1. Create component in `src/compositions/`
2. Register in `src/Root.tsx` with `<Composition>`
3. Set: id, component, durationInFrames, fps, width, height

## See References

- [codecs.md](references/codecs.md) - Encoding options
- [animations.md](references/animations.md) - Animation patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aviz85) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
