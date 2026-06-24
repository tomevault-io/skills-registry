---
name: gemini
description: Use Gemini CLI for web research, multimodal tasks (PDFs, images), or as a second opinion. Use when this capability is needed.
metadata:
  author: ykdojo
---

# Gemini CLI

Use Gemini for web research, multimodal tasks (PDFs, images), or as a second opinion.

## Usage

```bash
gemini --yolo --model gemini-3.1-pro-preview --prompt "Your task"
```

## Examples

Reddit research:
```bash
gemini -y -m gemini-3.1-pro-preview -p "Research what people on Reddit say about Claude Code"
```

Analyze an image:
```bash
gemini -y -m gemini-3.1-pro-preview -p "Describe this image: /path/to/image.png"
```

Analyze a PDF:
```bash
gemini -y -m gemini-3.1-pro-preview -p "Summarize this PDF: /path/to/doc.pdf"
```

## Notes

- File writes only work in current directory (not /tmp)
- Requires GEMINI_API_KEY env var

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ykdojo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
