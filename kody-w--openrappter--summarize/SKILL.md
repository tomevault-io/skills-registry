---
name: summarize
description: Summarize text, articles, documents, or web pages using AI models. Use when this capability is needed.
metadata:
  author: kody-w
---

# Summarize

Generate summaries of text content using AI.

## Summarize Text

Provide text directly and get a concise summary using the configured AI provider.

## Summarize a URL

```bash
curl -s "https://example.com/article" | \
  sed 's/<[^>]*>//g' | \
  head -500
# Then pass to AI for summarization
```

## Summarize a File

```bash
cat document.txt | head -1000
# Pass to AI for summarization
```

## Options

- **Length**: short (1-2 sentences), medium (paragraph), long (detailed)
- **Format**: bullet points, narrative, key takeaways
- **Focus**: main ideas, action items, technical details
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
