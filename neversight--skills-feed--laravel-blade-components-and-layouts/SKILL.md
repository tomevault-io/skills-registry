---
name: laravelblade-components-and-layouts
description: Compose UIs with Blade components, slots, and layouts; keep templates pure and testable Use when this capability is needed.
metadata:
  author: neversight
---

# Blade Components and Layouts

Encapsulate markup and behavior with components; prefer slots over includes.

## Commands

```
sail artisan make:component Alert              # or: php artisan make:component Alert

// Use component
<x-alert type="warning" :message="$msg" class="mb-4" />

// Layouts + stacks
@extends('layouts.app')
@push('scripts')
    <script>/* page script */</script>
@endpush
```

## Patterns

- Keep components dumb: pass data in, emit markup out
- Use `merge()` to honor passed classes/attributes in components
- Prefer named slots for readability
- Extract small, reusable atoms rather than giant organisms

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
