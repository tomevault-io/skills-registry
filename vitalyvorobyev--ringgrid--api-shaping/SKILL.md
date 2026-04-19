---
name: api-shaping
description: description: Use this when designing or refactoring public APIs across workspace crates. Keeps APIs small, explicit, and stable while allowing fast internals. Use when this capability is needed.
metadata:
  author: vitalyvorobyev
---
---
name: api-shaping
description: Use this when designing or refactoring public APIs across workspace crates. Keeps APIs small, explicit, and stable while allowing fast internals.
---------------------------------------------------------------------------------------------------------------------------------------------------------

# API shaping (lightweight)

## Aim

* Small public surface
* Clear data ownership (views vs owned)
* Fast internals without leaking complexity

## Prefer

* `Detector` structs that own scratch buffers → avoid allocations per call.
* `Config` structs with safe defaults, but don’t hide “magic” thresholds.
* `ImageView<T>` / `ImageViewMut<T>` in APIs; keep crates buffer-agnostic.
* Separate “core algorithm” from “pipeline convenience wrapper”.

## Avoid

* Generic abstractions that obscure hot loops.
* Exposing internal scratch buffers in public API.
* “One mega function” that does everything.

## Patterns that work here

* `detect_*(&mut self, img: &ImageView<_>, cfg: &Config) -> Output`
* `Output` types that can be iterated cheaply (`Vec<Edgel>`, `Vec<LaserSample>`)
* Optional features for parallelism/SIMD later, not in baseline.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vitalyvorobyev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
