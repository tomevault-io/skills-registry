---
name: docx
description: This skill should be used when the user asks to "create a Word document", "edit a docx", "extract text from docx", mentions ".docx", "Word document", "python-docx", or discusses Word document creation, editing, formatting, and content extraction. Use when this capability is needed.
metadata:
  author: ernestoelo
---

# DOCX Skill

Provides tools and workflows for:
- Creating and editing Word documents
- Extracting and analyzing content
- Automating document formatting and validation

## When to Use
- Any task involving .docx files (reports, memos, templates)
- Content extraction, tracked changes, or format conversion

## Usage Example
```bash
python scripts/office/soffice.py --headless --convert-to docx document.doc
```

## Advanced Example (docx-js)
```javascript
const { Document, Packer, Paragraph } = require('docx');
const doc = new Document({ sections: [{ children: [new Paragraph('Hello World')] }] });
Packer.toBuffer(doc).then(buffer => fs.writeFileSync('doc.docx', buffer));
```

## Validation
Use the provided validate.py script (see anthropic-examples/docx) to ensure document compliance.

## Best Practices
- Always set page size explicitly
- Use built-in heading styles for TOC
- Validate with validate.py and fix errors

## References
- See scripts/ for bundled examples from anthropic-examples/docx
- Follows architect, dev-workflow, and sys-env standards

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ernestoelo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
