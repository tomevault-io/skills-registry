---
name: gif-search
description: Search and download GIFs from Tenor and Giphy APIs Use when this capability is needed.
metadata:
  author: phuetz
---

# GIF Search

## Overview

Search for GIFs using Tenor or Giphy APIs and download them for sharing.

## Tenor (free tier, no key required for basic)

### Search
```bash
# Basic search (Tenor v2 API)
curl -s "https://tenor.googleapis.com/v2/search?q=thumbs+up&limit=5&key=${TENOR_API_KEY:-AIzaSyA...}" \
  | jq -r '.results[].media_formats.gif.url'
```

### Download
```bash
url=$(curl -s "https://tenor.googleapis.com/v2/search?q=celebration&limit=1&key=${TENOR_API_KEY}" \
  | jq -r '.results[0].media_formats.gif.url')
curl -s "$url" -o /tmp/celebration.gif
```

## Giphy (requires API key)

### Search
```bash
curl -s "https://api.giphy.com/v1/gifs/search?api_key=$GIPHY_API_KEY&q=thumbs+up&limit=5" \
  | jq -r '.data[].images.original.url'
```

### Trending
```bash
curl -s "https://api.giphy.com/v1/gifs/trending?api_key=$GIPHY_API_KEY&limit=5" \
  | jq -r '.data[].images.original.url'
```

## Tips

- URL-encode search queries: spaces become `+` or `%20`
- Use `jq` to filter results and extract URLs
- Download to `/tmp/` for temporary use
- Tenor's free tier allows basic searches without a key
- For smaller file sizes, use `mediumgif` or `tinygif` format from Tenor
- For Giphy, use `fixed_height` or `fixed_width` for smaller variants

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuetz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
