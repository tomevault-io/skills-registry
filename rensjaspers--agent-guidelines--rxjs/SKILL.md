---
name: rxjs
description: RxJS state management guidelines for keeping state inside streams and using pure transformations. Use when working with RxJS observables, Subjects, or reactive patterns. Use when this capability is needed.
metadata:
  author: rensjaspers
---

# RxJS

---

## State Management

- Keep all state inside the stream — no external mutation
- Avoid unnecessary Subjects; derive from existing streams
- Use pure transformations (`map`, `filter`, `switchMap`) over imperative logic

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rensjaspers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
