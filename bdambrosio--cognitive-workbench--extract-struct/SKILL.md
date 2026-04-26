---
name: extract-struct
description: Extract structured metadata (title, authors, year) from paper text using LLM. Use to convert online search results metadata to JSON Use when this capability is needed.
metadata:
  author: bdambrosio
---

# extract-struct

Extract structured metadata from academic paper text using LLM analysis.

## Input

- `target`: Note ID or variable containing full text or first pages of academic paper

## Output

Returns JSON Note with:
- `title`: Paper title
- `authors`: List of author names
- `year`: Publication year
- `venue`: Conference/journal if identifiable
- `abstract`: Paper abstract if present

## Behavior

- Uses LLM to analyze paper text and extract structured fields
- Handles various paper formats and layouts
- Returns only JSON, no explanation text

## Planning Notes

- Provide full text or first few pages for best results
- Works best with academic papers that have clear title/author sections
- Use with `fetch-text` to get paper content first

## Example

```json
{"type":"extract-struct","target":"$paper_text","out":"$metadata"}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
