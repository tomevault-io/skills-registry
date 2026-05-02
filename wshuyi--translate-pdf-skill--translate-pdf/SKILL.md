---
name: translate-pdf
description: | Use when this capability is needed.
metadata:
  author: wshuyi
---

# PDF Translation

Translate PDF text while preserving structure, colors, and background styling.

## Workflow

### Step 1: Extract texts

```bash
python {skill_path}/scripts/extract_texts.py <input.pdf>
```

Review output to see all unique text strings in the PDF.

### Step 2: Create translation mapping

Translate each text to target language. Create JSON file:

```json
{
  "Original Text 1": "Translated 1",
  "Original Text 2": "Translated 2"
}
```

Save as `translations.json` next to input PDF.

### Step 3: Apply translations

```bash
python {skill_path}/scripts/translate_pdf.py <input.pdf> translations.json <output.pdf> --font <fontname>
```

**Font options:**
| Font | Language |
|------|----------|
| `helv` | Latin (English, Spanish, Portuguese, French, German, etc.) |
| `china-ss` | Simplified Chinese |
| `china-ts` | Traditional Chinese |
| `japan` | Japanese |
| `korea` | Korean |

## Output naming

Append language suffix: `filename_EN.pdf`, `filename_ZH.pdf`, `filename_JA.pdf`

## Tips

- Keep proper nouns, abbreviations, technical terms unchanged when appropriate
- CJK fonts auto-scale to 90% for better fit
- Use transparent fill to preserve original background colors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wshuyi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
