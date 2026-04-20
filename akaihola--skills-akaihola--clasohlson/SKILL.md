---
name: clasohlson
description: >- Use when this capability is needed.
metadata:
  author: akaihola
---

# Clas Ohlson Finland Product Search

Search the Clas Ohlson Finland product catalog using their internal Voyado Elevate
(Apptus eSales) REST API. No browser or authentication required.

## Quick Start

Run the search script to find products:

```bash
./scripts/search.py "taskulamppu"
./scripts/search.py "led lamppu" --limit 20
./scripts/search.py "porakone" --json
```

## How It Works

The Clas Ohlson Finnish webshop at `clasohlson.com/fi/` uses a Voyado Elevate
(formerly Apptus eSales) search backend. The search script calls this API directly
over HTTPS with JSON responses, bypassing the need for a browser.

The API requires no authentication — only random UUID v4 values for session tracking.

## Using the Search Script

### Basic search

```bash
./scripts/search.py "search term"
```

Prints a formatted list of up to 10 products with name, brand, price, category,
and article number.

### JSON output

```bash
./scripts/search.py "search term" --json
```

Outputs the raw API response as JSON for programmatic use.

### Pagination

```bash
./scripts/search.py "search term" --limit 20 --offset 11
```

- `--limit N` — Number of results (default: 10)
- `--offset N` — Starting position, 1-based (default: 1)

### Custom attributes

```bash
./scripts/search.py "search term" --attributes name_fi,baseprice,brand
```

Override which product fields to request. See `references/api.md` for the full
list of available attributes.

## Programmatic Use

Import the search functions in Python:

```python
from scripts.search import search_products, extract_products

raw = search_products("lamppu", limit=5)
products = extract_products(raw)
for p in products:
    print(p["name_fi"], p.get("baseprice", "N/A"))
```

## Key Product Attributes

| Attribute              | Description                        |
|------------------------|------------------------------------|
| `name_fi`              | Product name in Finnish            |
| `baseprice`            | Price including VAT (EUR)          |
| `brand`                | Brand name                         |
| `mainCategoryName_fi`  | Primary category                   |
| `gridViewImage`        | Image path (relative)              |
| `article_number`       | Article number                     |
| `description_fi`       | Product description in Finnish     |
| `campaignStatus`       | Whether product is on sale         |

## Image URLs

Image paths from the API are relative. Prefix with:

```
https://images.clasohlson.com/medias
```

## API Reference

See `references/api.md` for full API documentation including endpoint details,
all parameters, response structure, and the autocomplete/suggestions endpoint.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akaihola) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
