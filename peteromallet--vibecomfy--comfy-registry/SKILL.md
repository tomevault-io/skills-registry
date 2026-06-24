---
name: comfy-registry
description: Use when searching for ComfyUI nodes, finding nodes by keyword/author/pack/category, discovering what nodes exist for a task, or browsing the node registry.
metadata:
  author: peteromallet
---

# ComfyUI Node Registry

Search and discover from 8400+ ComfyUI nodes.

## MCP Tools

| Tool | Purpose |
|------|---------|
| `comfy_search` | Search nodes by keyword - use this first |
| `comfy_spec` | Get full node spec (inputs, outputs, types) - use after finding a node |
| `comfy_author` | Find nodes by author (kijai, filliptm, Lightricks) |
| `comfy_categories` | Browse all node categories |
| `comfy_packs` | Browse all node packs |
| `comfy_stats` | Registry statistics |

## Search Examples

Search uses smart aliases - "ltx" expands to ltx, ltx2, lightricks, etc.

```
comfy_search("controlnet")      → ControlNet loaders and apply nodes
comfy_search("upscale model")   → Model-based upscalers
comfy_search("audio reactive")  → Audio-driven effect nodes (amplitude, rms, onset...)
comfy_search("ltx sampler")     → LTX video sampling nodes
comfy_search("mask grow")       → Mask expansion/dilation nodes
comfy_search("face")            → Face detection, swap, restore nodes
comfy_search("v2v")             → Video-to-video nodes (vid2vid, video2video...)
```

## Workflow

1. **Search**: `comfy_search("your task")`
2. **Inspect**: `comfy_spec("NodeName")` for promising results
3. **Browse**: Use `comfy_author`, `comfy_categories`, `comfy_packs` to explore

## Key Authors

| Author | Focus |
|--------|-------|
| kijai | KJNodes - workflow utils, masks, Set/Get |
| filliptm | Fill-Nodes - VFX, audio, video |
| Lightricks | LTX video generation |
| Kosinkadink | AnimateDiff Evolved |
| cubiq | IPAdapter |

## Setup

Requires MCP server. See [MCP_SETUP.md](../comfy-edit/MCP_SETUP.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/peteromallet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
