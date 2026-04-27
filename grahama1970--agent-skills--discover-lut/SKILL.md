---
name: discover-lut
description: > Use when this capability is needed.
metadata:
  author: grahama1970
---

# discover-lut

Search and retrieve cinematic 3D LUTs (.cube) for the Horus Movie Pipeline.

## Usage

```bash
./run.sh search "teal and orange" --output ./luts/
./run.sh list
./run.sh fetch "ID"
```

## Contract

- **Input**: Query string or LUT ID.
- **Output**: `.cube` file(s) in the specified directory.
- **Dependencies**: `dogpile` (for web search), `brave-search`.

## Strategy

1.  **Local Cache**: Check `.pi-cache/luts/` for previously found LUTs.
2.  **Web Search**: Use `dogpile` or `brave-search` to find open-source LUT collections (GitHub, blog posts).
3.  **Extraction**: If a URL is provided, try to find direct `.cube` download links.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grahama1970) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
