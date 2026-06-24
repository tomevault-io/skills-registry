---
name: libtransform
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libtransform Skill

## When to Use

- Converting PDF documents to HTML
- Extracting content from scanned documents
- Processing documents with LLM vision models
- Building document transformation pipelines

## Key Concepts

**pdfToHtml**: Splits PDF into page images, sends to vision-capable LLM, and
assembles HTML output with semantic structure.

## Usage Patterns

### Pattern 1: Convert PDF to HTML

```javascript
import { pdfToHtml } from "@copilot-ld/libtransform";

const pdfBuffer = await fs.readFile("document.pdf");
const html = await pdfToHtml(pdfBuffer, {
  model: "gpt-4-vision-preview",
  maxPages: 50,
});
```

## Integration

Used by libingest pipeline for document processing. Requires LLM with vision
capabilities.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
