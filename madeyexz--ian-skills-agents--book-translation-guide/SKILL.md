---
name: book-translation-guide
description: Workflow for translating entire books using AI agents. Split markdown books into chapters, batch translate 5 at a time, and verify completeness. Works with any language pair. Use when this capability is needed.
metadata:
  author: madeyexz
---

# Book Translation Guide for Agents

## Prerequisites

**IMPORTANT**: This skill only works if the book has already been parsed to Markdown format. The source file must be a `.md` file with proper heading structure using `#` symbols.

If the book is in PDF, EPUB, or other formats, it must first be converted to Markdown before using this translation workflow.

### Prompt Example (ZIP input: PDF + assets)

Use this starter prompt when your input is a ZIP file containing a book PDF and asset files:

```text
Translate this book to Traditional Chinese using:
- skills/book-translation-guide/SKILL.md
- agents/markdown-zh-tw-translator.md

https://github.com/madeyexz/ian-skills-agents

Input ZIP:
/path/to/book-package.zip

Requirements:
1. Unzip into a working folder.
2. If the source is PDF, convert it to markdown first (chaptered `.md` files).
3. Preserve markdown image attachments by keeping/copying asset folders and not changing image link targets.
4. Split oversized markdown chapters to ~40-60KB each.
5. Translate with sub-agents in batches of 5 files.
6. Merge translated parts and verify completeness with tail checks.
7. Output import-ready folders:
   - Book-Chapters/
   - Book-Chapters-ZH-TW/ (translated markdown + assets)
```

---

## Overview

This guide provides a systematic approach to translating entire books between **any language pair** using Claude Code translator agents.

**Supported translations include:**
- English → Traditional Chinese, Japanese, Korean, Spanish, French, German, etc.
- Any source language → Any target language

The workflow is language-agnostic; you simply need a translator agent configured for your target language.

---

## Step 1: Analyze Document Structure

First, understand the book's structure:

```bash
# Check file size and line count
wc -l "book.md" && ls -la "book.md"

# Find all chapter headings (lines starting with #)
grep "^# " "book.md"
```

This reveals:
- Total length of the book
- Number of chapters and their titles
- Starting line numbers for each chapter

---

## Step 2: Create Directory Structure

```bash
mkdir -p "Book-Chapters"            # For original chapter files
mkdir -p "Book-Chapters-Translated" # For translated chapter files
```

You can name the translated folder based on your target language:
- `Book-Chapters-ZH-TW` (Traditional Chinese)
- `Book-Chapters-JA` (Japanese)
- `Book-Chapters-ES` (Spanish)
- etc.

---

## Step 3: Split into Chapters

Use `sed` to extract chapters by line numbers:

```bash
sed -n 'START,ENDp' "book.md" > "Book-Chapters/00-Chapter-Name.md"
```

Example for a typical book:

```bash
sed -n '1,55p' "book.md" > "Book-Chapters/00-Front-Matter.md"
sed -n '56,593p' "book.md" > "Book-Chapters/01-Chapter-One.md"
sed -n '594,841p' "book.md" > "Book-Chapters/02-Chapter-Two.md"
# Continue for all chapters...
```

**CRITICAL**: Keep each file under **50-60KB** to avoid token limits.

---

## Step 4: Handle Oversized Chapters

If a chapter exceeds 60KB, split it further:

```bash
# Check chapter structure
grep "^# " "07-Large-Chapter.md"

# Split into parts at a logical section break
sed -n '1,231p' "07-Large-Chapter.md" > "07a-Part1.md"
sed -n '232,471p' "07-Large-Chapter.md" > "07b-Part2.md"
```

After translation, merge the parts:

```bash
cat "Book-Translated/07a-Part1.md" "Book-Translated/07b-Part2.md" > "Book-Translated/07-Large-Chapter.md"
rm "Book-Translated/07a-Part1.md" "Book-Translated/07b-Part2.md"
```

---

## Step 5: Batch Translation Strategy

Translate **5 chapters at a time**, waiting for completion before proceeding:

```
Batch 1: Chapters 00-04 → Wait for completion
Batch 2: Chapters 05-09 → Wait for completion
Batch 3: Chapters 10-14 → Wait for completion
...continue until done
```

Benefits:
- Avoids rate limits
- Allows early detection of issues
- Ensures consistent translation quality

---

## Step 6: Invoke Translation Agent

For each chapter, call the appropriate translation agent based on your target language:

### Example: English → Traditional Chinese (Taiwan)

```
Task(
  subagent_type: "markdown-zh-tw-translator",
  description: "Translate Chapter X to ZH-TW",
  prompt: "Translate the markdown file at /path/to/Book-Chapters/XX-Chapter.md
           from English to Traditional Chinese (繁體中文).

           Save the translated file to /path/to/Book-Chapters-ZH-TW/XX-Chapter.md

           Use your translation guidelines to ensure high quality translation
           while preserving markdown formatting."
)
```

### Example: English → Japanese

```
Task(
  subagent_type: "markdown-ja-translator",
  description: "Translate Chapter X to Japanese",
  prompt: "Translate the markdown file at /path/to/Book-Chapters/XX-Chapter.md
           from English to Japanese (日本語).

           Save the translated file to /path/to/Book-Chapters-JA/XX-Chapter.md

           Use your translation guidelines to ensure high quality translation
           while preserving markdown formatting."
)
```

### Example: French → English

```
Task(
  subagent_type: "markdown-en-translator",
  description: "Translate Chapter X to English",
  prompt: "Translate the markdown file at /path/to/Book-Chapters/XX-Chapter.md
           from French to English.

           Save the translated file to /path/to/Book-Chapters-EN/XX-Chapter.md

           Use your translation guidelines to ensure high quality translation
           while preserving markdown formatting."
)
```

Run 5 agents in parallel for each batch.

---

## Step 7: Verify Translation Completeness

After all chapters are translated, perform **side-by-side verification** comparing the last 10 lines of each chapter between original and translated versions.

### Comprehensive Side-by-Side Verification

For each chapter, compare original and translated endings in parallel:

```bash
echo "=== 01-Chapter ===" && \
echo "-- Original --" && \
tail -10 "Book-Chapters/01-Chapter.md" && \
echo -e "\n-- Translated --" && \
tail -10 "Book-Chapters-Translated/01-Chapter.md"
```

Run this for **all chapters in parallel** to verify:
- Translation reaches the end of each chapter (no truncation)
- Final paragraphs match in meaning
- Reference links and citations are preserved
- Formatting (headers, lists, links) is intact

### Bulk Verification Script

```bash
for chapter in Book-Chapters/*.md; do
  name=$(basename "$chapter")
  echo "=== $name ==="
  echo "-- Original --"
  tail -10 "$chapter"
  echo -e "\n-- Translated --"
  tail -10 "Book-Chapters-Translated/$name"
  echo -e "\n"
done
```

### What to Check

| Element | Verification |
|---------|--------------|
| Content completeness | Last sentences match in meaning |
| Reference links | URLs preserved unchanged |
| Section numbers | `[1a]`, `[2b3]` etc. preserved |
| Proper nouns | Names unchanged in both versions |
| Technical terms | Bilingual format present: 中文 (English) |
| Markdown formatting | Headers, links, emphasis intact |

### Quick Verification (Single Chapter)

```bash
# Compare original ending
tail -5 "Book-Chapters/01-Chapter.md"

# Compare translated ending
tail -5 "Book-Chapters-Translated/01-Chapter.md"
```

---

## Translation Quality Standards

General principles that apply to **all languages**:

| Element | Handling |
|---------|----------|
| Personal names | Keep original (with exceptions based on target language conventions) |
| Technical terms | Bilingual format recommended: 翻訳 (Translation) |
| Markdown formatting | Preserve all: headers, images, links, footnotes |
| Code blocks | Never translate code |
| LaTeX/Math | Keep original notation |

### Language-Specific Considerations

**Traditional Chinese (Taiwan):**
- Use Taiwan conventions: 軟體, 程式, 資料, 網路

**Japanese:**
- Use appropriate politeness levels
- カタカナ for loanwords

**Spanish:**
- Consider regional variations (Spain vs Latin America)
- Appropriate formality (tú vs usted)

**German:**
- Compound word handling
- Formal vs informal address (Sie vs du)

---

## Complete Workflow Diagram

```
Original File (book.md)
        ↓
[1] Analyze Structure (grep chapter headings)
        ↓
[2] Create Directories
        ↓
[3] Split into Chapters (sed by line numbers)
        ↓
[4] Check File Sizes (split if >60KB)
        ↓
[5] Batch Translate (5 chapters per batch)
        ↓
[6] Merge Split Chapters (if any)
        ↓
[7] Verify Completeness (tail comparison)
        ↓
[8] Done - All chapters translated
```

---

## Troubleshooting

### Rate Limit Errors
- Reduce batch size from 5 to 3 chapters
- Wait longer between batches

### Token Limit Exceeded
- Split the chapter into smaller parts
- Target 40-50KB per file instead of 60KB

### Incomplete Translation
- Check if agent hit limits mid-translation
- Re-run translation for that specific chapter
- Verify by comparing file endings

### Missing Chapters
- List output directory to identify gaps
- Re-translate missing files individually

### Wrong Language Conventions
- Create a custom translator agent with specific rules for your target language/region

---

## Example: Real Book Translations

| Book | Source | Target | Size | Chapters |
|------|--------|--------|------|----------|
| 21 Lessons for the 21st Century | EN | ZH-TW | 714 KB | 25 |
| Homo Deus | EN | ZH-TW | 960 KB | 16 |
| Augmenting Human Intellect (Engelbart, 1962) | EN | ZH-TW | 322 KB | 9 |

---

## Notes

1. Always verify the source markdown has proper `#` heading structure
2. Back up original files before starting
3. Keep a checklist of chapters to track progress
4. For books >1MB, consider translating over multiple sessions
5. Create or configure a translator agent for your target language before starting
6. The workflow is the same regardless of language pair - only the translator agent changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madeyexz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
