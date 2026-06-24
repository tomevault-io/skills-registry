---
name: gifgrep
description: Search and retrieve GIFs from Giphy and Tenor APIs. Use when this capability is needed.
metadata:
  author: kody-w
---

# GifGrep

Search for GIFs using Giphy and Tenor.

## Search Giphy

```bash
curl -s "https://api.giphy.com/v1/gifs/search?api_key=$GIPHY_API_KEY&q=funny+cat&limit=5" | jq '.data[].url'
```

## Trending GIFs

```bash
curl -s "https://api.giphy.com/v1/gifs/trending?api_key=$GIPHY_API_KEY&limit=10" | jq '.data[].url'
```

## Random GIF

```bash
curl -s "https://api.giphy.com/v1/gifs/random?api_key=$GIPHY_API_KEY&tag=celebration" | jq '.data.url'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
