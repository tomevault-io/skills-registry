---
name: deep-dive-notes
description: Transcribe handwritten multi-source research notes from reMarkable PDFs or paper photos. Trigger phrase "process deep dive notes". Handles research compilations with multiple sources per document. Converts handwriting to markdown, applies formatting conventions, generates metadata, creates AI summaries, and appends quotes to a central quotes file. Notes may be in Czech or English. Use when this capability is needed.
metadata:
  author: erikvanek
---

# Deep Dive Notes Processor

Transcribe handwritten multi-source research notes into structured Obsidian notes.

## Trigger

User says "process deep dive notes" and provides either:
- A PDF file (reMarkable export)
- Multiple image files (photos of paper notes)

## Language

Notes may be in **Czech or English**. Preserve the original language throughout. Do not translate.

## Workflow

### 1. Convert and Transcribe

**For PDF input:**
```python
from pdf2image import convert_from_path
images = convert_from_path('notes.pdf', dpi=150)
for i, img in enumerate(images):
    img.save(f'page_{i+1}.png', 'PNG')
```

**For image input:**
Accept images directly and process in order.

Then visually read and transcribe each page.

### 2. Verify Content Type

Deep dive notes contain **multiple sources** (multiple H1 headings), each representing a different article, video, or resource.

If user provides single-source notes, suggest: "This looks like single-source book notes. Should I use 'process book notes' instead?"

### 3. Apply Formatting Rules

See `../shared/formatting-rules.md` for complete conventions. Key rules:

- `!` at bullet start → *italics*
- `!!!` at bullet start → **bold**
- Underscored words → **bold**
- Numbered sequences → numbered lists
- H1 (`#`) marks source titles
- Quotes: `Author: *"Quote text"*`

### 4. Collect Metadata

Ask user for:
- **Resources list** (URLs to original sources)

Then query Obsidian for existing tags:
```
obsidian-mcp-tools:search_vault_simple with relevant keywords
```
Select 3-5 tags that match the vault's existing taxonomy.

### 5. Generate Summary

If transcribed content exceeds ~1 A4 of text, generate a 1-3 paragraph summary covering:
- Core tension or theme
- Key frameworks or approaches
- Critical questions or insights

Place summary after frontmatter, before raw transcription.

### 6. Build Frontmatter

```yaml
---
title: [Derived from content or user input]
date_created: [YYYY-MM-DD]
source_type: research
tags:
  - [3-5 relevant tags from vault]
resources:
  - [URL 1]
  - [URL 2]
---
```

### 7. Handle Quotes

If transcription contains quotes (format: `Author: "Quote text"`):

In transcription:
```markdown
- Author: *"Quote text"*
```

Append to Obsidian quotes file `10 - 🧠 Knowledge/3 - 📚 Resources/Learning/Quotes.md`:
```markdown
- Author: "Quote text"
```

### 8. Save to Obsidian

Create file in inbox: `02 - 📩 Inbox/[Title].md`

Use `obsidian-mcp-tools:create_vault_file` with the complete markdown content.

### 9. Add Related Notes

Query Obsidian for related existing notes and add a `## Related notes` section with wiki-links at the end.

## Output Structure

```markdown
---
[frontmatter]
---

## Summary

[AI-generated summary if content is long enough]

---

# From "[Source 1]"

- [transcribed bullets with formatting]
- *important point*
- **very important point**
- Author: *"Quote text"*

# From "[Source 2]"

...

## Related notes

- [[Related Note 1]]
- [[Related Note 2]]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erikvanek) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
