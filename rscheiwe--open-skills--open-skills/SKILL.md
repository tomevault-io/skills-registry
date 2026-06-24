---
name: stats
description: Statistics about the text Use when this capability is needed.
metadata:
  author: rscheiwe
---

# Text Summarizer Skill

A more complex example that demonstrates text processing capabilities.

## What it does

This skill takes a long piece of text and:
1. Analyzes the text (word count, sentence count, etc.)
2. Extracts key points
3. Creates a bullet-point summary
4. Generates a statistics report

## Usage

### Input

```json
{
  "text": "Your long text here...",
  "max_points": 5
}
```

### Output

```json
{
  "summary": "• Point 1\n• Point 2\n• Point 3",
  "stats": {
    "word_count": 150,
    "sentence_count": 8,
    "avg_sentence_length": 18.75
  }
}
```

## Artifacts

- `summary.md`: Markdown file with the formatted summary
- `stats.json`: JSON file with detailed statistics

## Algorithm

This is a simple implementation that:
1. Splits text into sentences
2. Scores sentences by length and position
3. Selects top N sentences as summary points

*Note: This is a demonstration. For production use, consider using NLP libraries like spaCy or transformers.*

---
> Source: [rscheiwe/open-skills](https://github.com/rscheiwe/open-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
