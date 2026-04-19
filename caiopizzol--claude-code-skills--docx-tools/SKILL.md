---
name: docx-tools
description: Extract, inspect, and compare .docx files by examining their XML structure. Use when analyzing Word documents, comparing versions, or debugging document issues. Use when this capability is needed.
metadata:
  author: caiopizzol
---

# DOCX Tools

Extract, inspect, and compare .docx files by examining their underlying XML structure. Since .docx files are ZIP archives containing XML, we use standard Unix tools.

## Installation

```bash
# macOS - tree is optional, other tools are pre-installed
brew install tree

# Ubuntu/Debian
sudo apt install unzip libxml2-utils tree
```

---

## Quick Reference

| Task | Command |
|------|---------|
| List contents | `unzip -l file.docx` |
| View document XML | `unzip -p file.docx word/document.xml \| xmllint --format -` |
| Extract to directory | `unzip -o -d /tmp/docx-out file.docx` |
| Compare content | `diff <(unzip -p a.docx word/document.xml \| xmllint --format -) <(unzip -p b.docx word/document.xml \| xmllint --format -)` |
| Compare structure | `diff <(unzip -l a.docx) <(unzip -l b.docx)` |

---

## Inspection Workflow

**Step 1: List archive contents**
```bash
unzip -l document.docx
```

**Step 2: View document structure (after extraction)**
```bash
unzip -o -d /tmp/docx-out document.docx
tree /tmp/docx-out
```

**Step 3: View formatted XML content**
```bash
unzip -p document.docx word/document.xml | xmllint --format -
```

---

## Comparison Workflow

**Step 1: Quick structure check**
```bash
unzip -l document-v1.docx
unzip -l document-v2.docx
```

**Step 2: Compare main document content**
```bash
diff -u <(unzip -p document-v1.docx word/document.xml | xmllint --format -) \
        <(unzip -p document-v2.docx word/document.xml | xmllint --format -)
```

**Step 3: Compare styles if formatting changed**
```bash
diff -u <(unzip -p document-v1.docx word/styles.xml | xmllint --format -) \
        <(unzip -p document-v2.docx word/styles.xml | xmllint --format -)
```

---

## Extraction Commands

```bash
# Extract entire .docx to temp directory
unzip -o -d /tmp/docx-extracted document.docx

# Extract specific file only
unzip -o -d /tmp/docx-extracted document.docx word/document.xml

# Pipe extraction (no temp files) - view without extracting
unzip -p document.docx word/document.xml

# List archive contents with details
unzip -l document.docx
```

---

## Comparison Commands

```bash
# Unified diff (recommended)
diff -u <(unzip -p a.docx word/document.xml | xmllint --format -) \
        <(unzip -p b.docx word/document.xml | xmllint --format -)

# Side-by-side comparison
diff -y <(unzip -p a.docx word/document.xml | xmllint --format -) \
        <(unzip -p b.docx word/document.xml | xmllint --format -)

# Check if files differ (quiet mode)
diff -q <(unzip -p a.docx word/document.xml | xmllint --format -) \
        <(unzip -p b.docx word/document.xml | xmllint --format -)

# Compare file lists between documents
diff <(unzip -l a.docx | awk 'NR>3 {print $4}' | sort) \
     <(unzip -l b.docx | awk 'NR>3 {print $4}' | sort)
```

---

## Inspection Commands

```bash
# View metadata (author, dates)
unzip -p document.docx docProps/core.xml | xmllint --format -

# View app info (word count)
unzip -p document.docx docProps/app.xml | xmllint --format -

# Show structure (after extraction)
tree /tmp/docx-out

# Show only XML files
tree /tmp/docx-out -P '*.xml'
```

---

## Key XML Files

| File | Contains |
|------|----------|
| `word/document.xml` | Main content (paragraphs, text) |
| `word/styles.xml` | Style definitions |
| `word/numbering.xml` | List formatting |
| `word/_rels/document.xml.rels` | Links/images references |
| `docProps/core.xml` | Author, dates, title |
| `docProps/app.xml` | Word count, application info |
| `[Content_Types].xml` | MIME type mappings |

---

## Troubleshooting

**Large diff output**: Pipe to `head -100` to limit lines.

**Namespace noise**: Focus on `<w:t>` tags for actual text content.

**Binary comparison errors**: Skip `word/media/` directory - compare XML only.

**Empty xmllint output**: Check file path inside archive with `unzip -l`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/caiopizzol) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
