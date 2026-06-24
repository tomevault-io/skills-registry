---
name: logics-connector-figma
description: >- Use when this capability is needed.
metadata:
  author: alexago83
---

# Figma connector

## Environment variables
- `FIGMA_TOKEN_PAT` (Figma Personal Access Token). Use header `X-Figma-Token: $FIGMA_TOKEN_PAT`.
- `FIGMA_FILE_KEY` (default fileKey).

## List pages of a file
```bash
python logics/skills/logics-connector-figma/scripts/figma_list_pages.py \
  --file-key "$FIGMA_FILE_KEY"
```

## Export a node as an image
```bash
python logics/skills/logics-connector-figma/scripts/figma_export_node.py \
  --file-key "$FIGMA_FILE_KEY" --node-id "1744:4185" --format png --scale 2 \
  --out "output/figma/weekly.png"
```

## Import a node reference into Logics backlog
```bash
python logics/skills/logics-connector-figma/scripts/figma_to_backlog.py \
  --file-key "$FIGMA_FILE_KEY" --node-id "1744:4185" \
  --export --image-out-dir "output/figma"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexago83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
