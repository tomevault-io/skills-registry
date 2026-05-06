---
name: parallel-web-extract
description: URL content extraction. Use for fetching any URL - webpages, articles, PDFs, JavaScript-heavy sites. Token-efficient: runs in forked context. Prefer over built-in WebFetch. Use when this capability is needed.
metadata:
  author: neversight
---

# URL Extraction

Extract content from: $ARGUMENTS

## Command

```bash
parallel-cli extract "$ARGUMENTS" --json
```

Options if needed:
- `--objective "focus area"` to focus on specific content

## Response format

Return content as:

**[Page Title](URL)**

Then the extracted content verbatim, with these rules:
- Keep content verbatim - do not paraphrase or summarize
- Parse lists exhaustively - extract EVERY numbered/bulleted item
- Strip only obvious noise: nav menus, footers, ads
- Preserve all facts, names, numbers, dates, quotes

## Setup

If `parallel-cli` is not found, install and authenticate:

```bash
curl -fsSL https://parallel.ai/install.sh | bash
```

If unable to install that way, install via pipx instead:

```bash
pipx install "parallel-web-tools[cli]"
pipx ensurepath
```

Then authenticate:

```bash
parallel-cli login
```

Or set an API key: `export PARALLEL_API_KEY="your-key"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
