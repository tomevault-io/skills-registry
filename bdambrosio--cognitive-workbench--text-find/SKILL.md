---
name: text-find
description: Locate pattern or substring and return position with context. Use to search for specific substring or pattern in a text document Use when this capability is needed.
metadata:
  author: bdambrosio
---

# text-find

Locate patterns or substrings in text and return matches with context.

## Input

- `target`: Note ID or variable containing text to search
- `pattern`: Text string or regex pattern to find (required)
- `context_lines`: Number of lines before/after to include (optional, default: 1)

## Output

Returns Note containing match results:
- Match positions (line and character)
- Matched text
- Surrounding context
- Count of matches

Returns null if no matches found.

## Behavior

- Case-sensitive by default
- Supports standard regex syntax
- Returns all matches, not just first
- Context helps understand match relevance

## Planning Notes

- Use simple text search for exact substring matching
- Use regex patterns for flexible pattern matching (e.g., email addresses, dates)

## Examples

Simple text search:
```json
{"type":"text-find","target":"$document","pattern":"TODO","out":"$todo_locations"}
```

Pattern search with context:
```json
{"type":"text-find","target":"$log","pattern":"ERROR.*timeout","context_lines":3,"out":"$errors"}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
