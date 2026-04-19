---
name: vowel-upper
description: Writes Python strings with all vowels converted to uppercase. Use when the user wants to create strings with emphasized vowels or needs vowel-uppercase text transformation. Use when this capability is needed.
metadata:
  author: davidbrownell
---

# Vowel Uppercase String Generator

Convert the provided text to a Python string where all vowels (a, e, i, o, u) are converted to uppercase.

## Input

The text to convert is provided as: `$ARGUMENTS`

## Instructions

1. Take the input text exactly as provided
2. Convert all lowercase vowels (a, e, i, o, u, y) to uppercase (A, E, I, O, U, Y)
3. Keep uppercase vowels as uppercase
4. Keep all other characters unchanged
5. Output the result as a valid Python string literal (with quotes)

## Output Format

Return the converted string as a Python string literal:

```python
"converted string here"
```

## Examples

| Input | Output |
|-------|--------|
| `hello world` | `"hEllO wOrld"` |
| `python programming` | `"pYthOn prOgrAmmIng"` |
| `AEIOUY` | `"AEIOUY"` |
| `xyz` | `"xYz"` |
| `The quick brown fox` | `"ThE qUIck brOwn fOx"` |

## Edge Cases

- If no text is provided, ask the user for the text to convert
- Empty string returns `""`
- Text with no vowels returns the original text as a Python string
- Preserve all whitespace and punctuation exactly as provided

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidbrownell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
