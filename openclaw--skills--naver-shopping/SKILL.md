---
name: naver-shopping
description: Search for products on Naver Shopping. Use when the user wants to find product prices, links, or compare items in the Korean market. Use when this capability is needed.
metadata:
  author: openclaw
---

# Naver Shopping Search

Use this skill to search for products on Naver Shopping using the Naver Search API.

## Usage

Run the search script with a query:

```bash
/Users/dryoo/.openclaw/workspace/skills/naver-shopping/scripts/search_shopping.py "상품명"
```

### Options

- `--display <number>`: Number of results to show (default: 5, max: 100)
- `--sort <sim|date|asc|dsc>`: Sort order (sim: similarity, date: date, asc: price ascending, dsc: price descending)

### Example

```bash
/Users/dryoo/.openclaw/workspace/skills/naver-shopping/scripts/search_shopping.py "아이폰 16" --display 3 --sort asc
```

## Environment Variables

Requires the following in `.env`:
- `NAVER_Client_ID`
- `NAVER_Client_Secret`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
