---
name: code-stats
description: Visualizes repository complexity by counting files, lines of code, and grouping by extension. Use to assess project size or growth. Use when this capability is needed.
metadata:
  author: openclaw
---

# Code Stats

Analyzes the current workspace to provide development metrics.

## Usage

```bash
node skills/code-stats/index.js [path]
```

Defaults to current working directory if path is omitted.

## Output

JSON object with:
- `files`: Total file count.
- `lines`: Total line count (approximate).
- `byExt`: Breakdown by file extension.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
