---
name: visual-design
description: | Use when this capability is needed.
metadata:
  author: plurigrid
---

# Visual design

Image generation and presentations.

## Image generation

Create and edit images from the command line using nanobanana CLI.

```bash
npm install -g @factory/nanobanana
export GEMINI_API_KEY="your-key"

nanobanana generate "company logo" --count=4 --styles=modern,minimal
nanobanana edit photo.png "remove background"
nanobanana icon "settings gear" --style=flat
nanobanana diagram "auth flow" --type=flowchart
```

Handles: logos, icons, diagrams, patterns, photo restoration, UI assets, visual sequences.

See: [image-generation.md](./image-generation.md)

## Presentations

Create slides using Slidev, a markdown-based presentation tool.

```bash
npm init slidev@latest
slidev                    # dev server
slidev export --format pptx   # export to PowerPoint
slidev build              # build as hostable SPA
```

Write slides in markdown, get code highlighting, animations, diagrams, and Vue components.

See: [presentations.md](./presentations.md) and [reference-slide-example.md](./reference-slide-example.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plurigrid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
