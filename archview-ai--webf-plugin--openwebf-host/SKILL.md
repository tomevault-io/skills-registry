---
name: openwebf-host
description: DEPRECATED umbrella Skill (backward compatibility). Use only when a Flutter host request spans multiple WebF integration areas (controllers + bundles + embedding + routing + caching/theming + perf). Prefer focused openwebf-host-* Skills. Use when this capability is needed.
metadata:
  author: archview-ai
---

# OpenWebF Host Skill

## Deprecated

Prefer these focused Skills:
- `openwebf-host-bundle-loading`
- `openwebf-host-controller-manager`
- `openwebf-host-embedding-constraints`
- `openwebf-host-hybrid-routing`
- `openwebf-host-theming-sync`
- `openwebf-host-caching-httpcachemode`
- `openwebf-host-performance-metrics`

## Instructions

If this umbrella Skill is active, route to the most relevant focused Skill using trigger terms:
- `WebFBundle` → `openwebf-host-bundle-loading`
- `WebFControllerManager` / “pre-render” → `openwebf-host-controller-manager`
- “constraints”, “ListView”, “unbounded” → `openwebf-host-embedding-constraints`
- `WebFSubView` / `go_router` → `openwebf-host-hybrid-routing`
- “theme sync”, “prefers-color-scheme” → `openwebf-host-theming-sync`
- `HttpCacheMode` / “stale content” → `openwebf-host-caching-httpcachemode`
- `dumpLoadingState` / FP/FCP/LCP → `openwebf-host-performance-metrics`

Keep fixes minimal, explain tradeoffs, and confirm store compliance when remote updates are involved.

If repo context is missing, start with `/webf:doctor` (optionally with a path) and then route to the focused Skill.

## Examples

- “Add WebF to this Flutter app and load a remote bundle with a safe caching strategy.”
- “Fix my layout: WebF inside a ListView causes constraint errors.”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archview-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
