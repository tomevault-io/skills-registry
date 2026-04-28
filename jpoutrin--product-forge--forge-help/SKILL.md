---
name: forge-help
description: Show all available Product Forge agents, skills, and commands Use when this capability is needed.
metadata:
  author: jpoutrin
---

# forge-help

Display the Product Forge index - a quick reference for all available tools.

## Usage

```
/forge-help              # Show full index
/forge-help security     # Filter by keyword
/forge-help django       # Find Django-related tools
```

## Execution

Read and display `forge-index.md` from this plugin directory.

If a search term is provided, filter to show only matching entries.

## Regenerating the Index

Run the script to update the index after adding new agents/skills/commands:

```bash
python scripts/generate-forge-index.py --plugins-dir plugins --output plugins/product-design/forge-index.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpoutrin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
