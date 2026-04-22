---
name: markdown-expert-writer
description: Use when creating or editing markdown files to ensure strict compliance with markdownlint rules and professional markdown best practices
metadata:
  author: donellmccoy
---

# Markdown Expert Writer (Strict Markdownlint Compliance)

## Overview

This skill enables writing markdown files that strictly comply with all 30+ markdownlint rules. Markdownlint enforces consistency, readability, and compatibility across markdown parsers (kramdown, GitHub, CommonMark). Every markdown file created or edited must pass markdownlint validation with zero violations.

**Core principle:** Perfect markdown is consistent, readable, and parser-compatible. Every violation of a markdownlint rule represents a choice that reduces one of these qualities.

## Markdownlint Rules Reference (All 30+ Rules)

This section documents all rules with violations to avoid. Rules are grouped by category for easy reference.

### Headers (MD001-MD026)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD001** | Skip header levels (e.g., h1 → h3) | Increment by exactly one level | ❌ `# H1` → `### H3` ✅ `# H1` → `## H2` → `### H3` |
| **MD002** | First header isn't h1 | Start file with `# Title` | ❌ `## Header` at start ✅ `# Title` first |
| **MD003** | Inconsistent header style (atx vs setext) | Pick one style and stick with it | ❌ `# ATX` then `Setext\n=====` ✅ Consistent `# H1` `## H2` |
| **MD018** | No space after hash in atx header | Always: `# Header` not `#Header` | ❌ `#Header 1` ✅ `# Header 1` |
| **MD019** | Multiple spaces after hash in atx | Exactly one space: `# Header` | ❌ `#  Header` ✅ `# Header` |
| **MD020** | No space inside closed atx hashes | Both sides: `# Header #` | ❌ `#Header#` ✅ `# Header #` |
| **MD021** | Multiple spaces inside closed atx | Exactly one: `# Header #` | ❌ `#  Header  #` ✅ `# Header #` |
| **MD022** | Headers not surrounded by blank lines | Add blank line before/after (except edges) | ❌ `# Header\nText` ✅ `# Header\n\nText` |
| **MD023** | Header indented by spaces | Headers must start at column 0 | ❌ `  # Header` ✅ `# Header` |
| **MD024** | Multiple headers with same text | Each header must have unique text | ❌ `# Overview` ... `# Overview` ✅ Make each title unique |
| **MD025** | Multiple h1 headers | Only one top-level header per document | ❌ `# Title` ... `# Another` ✅ One `# Title`, rest are `## H2` |
| **MD026** | Header ends with punctuation | Remove trailing punctuation | ❌ `# Header.` ✅ `# Header` |

### Lists (MD004-MD007, MD029-MD030)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD004** | Inconsistent unordered list markers | Use same symbol throughout (*, +, or -) | ❌ `* Item` then `- Item` ✅ `* Item 1` `* Item 2` |
| **MD005** | Misaligned list items at same level | Indent consistently (3 or 4 spaces) | ❌ Mixed indentation ✅ All 3 spaces for nested |
| **MD006** | Top-level list indented | Start lists at column 0 | ❌ `  * Item` (indented) ✅ `* Item` (at start) |
| **MD007** | Wrong list indentation depth | Default 3 spaces (or 4 spaces per config) | ❌ `* Item\n  * Nested` (2 spaces) ✅ `* Item\n   * Nested` (3 spaces) |
| **MD029** | Ordered list prefix wrong | Use `1.` prefix consistently (default) | ❌ `1. Item` `2. Item` ✅ `1. Item` `1. Item` |
| **MD030** | Wrong spaces after list marker | Exactly 1 space after marker (default) | ❌ `*  Item` (2 spaces) ✅ `* Item` (1 space) |

### Code Blocks (MD031, MD040, MD046)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD031** | Code block not surrounded by blank lines | Add blank line before/after fenced code | ❌ `Text\n\`\`\`\nCode` ✅ `Text\n\n\`\`\`\nCode\n\`\`\`` |
| **MD040** | Code block missing language | Always specify language (or `text`) | ❌ `\`\`\`\nCode\n\`\`\`` ✅ `\`\`\`csharp\nCode\n\`\`\`` |
| **MD046** | Code block style inconsistent | Use fenced blocks (not indented) | ❌ `    Code` (indented) ✅ `\`\`\`\nCode\n\`\`\`` (fenced) |

### Emphasis & Links (MD011, MD036-MD039)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD011** | Reversed link syntax | Use `[text](url)` not `(text)[url]` | ❌ `(Text)[http://example.com]` ✅ `[Text](http://example.com)` |
| **MD034** | Bare URL without angle brackets | Wrap URLs: `<http://example.com>` | ❌ `See http://example.com` ✅ `See <http://example.com>` |
| **MD036** | Bold/italic used instead of header | Use headers for sections, not **bold** | ❌ `**Section**` (emphasized) ✅ `## Section` (header) |
| **MD037** | Spaces inside emphasis markers | Remove spaces: `**bold**` not `** bold **` | ❌ `** bold **` ✅ `**bold**` |
| **MD038** | Spaces inside code backticks | Remove spaces: `` `code` `` not `` ` code ` `` | ❌ `` ` code ` `` ✅ `` `code` `` |
| **MD039** | Spaces inside link text | Remove spaces: `[link](url)` not `[ link ](url)` | ❌ `[ link ](url)` ✅ `[link](url)` |

### Whitespace (MD009-MD010, MD012, MD027-MD028)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD009** | Trailing spaces at end of line | Remove all trailing whitespace | ❌ `Text   ` (3 spaces at end) ✅ `Text` (no trailing spaces) |
| **MD010** | Hard tabs instead of spaces | Replace tabs with spaces (2, 3, or 4) | ❌ `\t* Item` (tab) ✅ `    * Item` (spaces) |
| **MD012** | Multiple consecutive blank lines | Max 1 blank line between sections | ❌ `Text\n\n\n\nMore` ✅ `Text\n\nMore` |
| **MD027** | Multiple spaces after blockquote marker | Exactly one space: `> Quote` | ❌ `>  Quote` (2 spaces) ✅ `> Quote` (1 space) |
| **MD028** | Blank line inside blockquote (adjacent blocks) | Add separator text or continue with `>` | ❌ `> Quote\n\n> Quote2` ✅ `> Quote\n>\n> Continuation` |

### Blockquotes & Horizontal Rules (MD035)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD035** | Inconsistent horizontal rule style | Use same symbol throughout document | ❌ `---` then `***` ✅ All `---` or all `***` |

### Inline HTML (MD033)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD033** | Raw HTML tags in markdown | Use pure markdown instead | ❌ `<h1>Title</h1>` ✅ `# Title` |

### Code Commands (MD014)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD014** | Dollar signs in code blocks without output | Remove `$` unless showing command+output | ❌ `$ ls` (no output shown) ✅ `ls` or show: `$ ls\nfoo bar` |

### Blank Lines Around Blocks (MD032)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD032** | Lists not surrounded by blank lines | Add blank line before/after lists | ❌ `Text\n* Item` ✅ `Text\n\n* Item\n\nMore` |

### File Structure (MD041, MD047)

| Rule | Problem | Solution | Example |
|------|---------|----------|---------|
| **MD041** | File doesn't start with h1 header | First line must be `# Title` | ❌ File starts with text ✅ `# Title` first line |
| **MD047** | File doesn't end with newline | Add final newline after last content | ❌ File ends: `Text[EOF]` ✅ File ends: `Text\n[EOF]` |
| **MD013** | Line too long (>80 chars) | Break long lines, wrap at ~80 chars | ❌ Very long lines ✅ Wrap to 80 char target |

## Perfect Markdown Pattern

Here is a complete markdown file that violates ZERO markdownlint rules:

```markdown
# Document Title

## Overview

This is the main overview section. Markdown must follow strict rules for consistency and parser compatibility.

### Subsection

This is a subsection with explanation.

## Key Concepts

Here are the key points:

* First item
* Second item
* Third item

## Code Example

Here's a code example:

```csharp
public static void HelloWorld()
{
    Console.WriteLine("Hello, world!");
}
```

## Lists

Both ordered and unordered lists are supported:

1. First step
1. Second step
1. Third step

### Nested Lists

Proper nesting with 3 spaces:

* Item 1
   * Nested 1.1
   * Nested 1.2
* Item 2
   * Nested 2.1

## Links and References

Here are proper links:

* [Link text](https://example.com)
* <https://example.com>

## Code Inline

Use backticks for inline code: `const x = 5;`

## Blockquotes

Proper blockquotes with blank lines:

> This is a blockquote.
>
> This is the same blockquote continued.

Here's text after the blockquote.

## Emphasis

Proper emphasis without spaces:

* **Bold text** should not have spaces inside
* *Italic text* should not have spaces inside

## Horizontal Rule

Use consistent horizontal rules:

---

## Conclusion

This document follows all markdownlint rules.
```

**Key observations about perfect markdown:**
1. **First line is h1 header** (`# Document Title`)
2. **Headers increment by 1** (h1 → h2 → h3, never h1 → h3)
3. **Headers surrounded by blank lines** (blank before and after)
4. **No trailing punctuation** on headers
5. **Single space after hash** (`# Title` not `#Title` or `#  Title`)
6. **Consistent list markers** (all `*` or all `-`, never mixed)
7. **3-space indentation** for nested lists (or configured default)
8. **Blank lines around lists** (before first item, after last item)
9. **Fenced code blocks with language** (`` ```python `` not `` ``` ``)
10. **Blank lines around code blocks**
11. **No trailing spaces** at end of lines
12. **No hard tabs** (use spaces)
13. **Links in brackets/parentheses** (`[text](url)`)
14. **No multiple blank lines** (max 1 between sections)
15. **Single newline at end of file**

## Practical Rules for Compliance

### Rule #1: Start Every File Correctly

```markdown
# File Title

This is the first content after the title.
```

**Every file MUST:**
- ✅ Start with `# Title` (h1 header)
- ✅ Have blank line after title
- ✅ Have exactly one space after `#`
- ✅ Have no trailing punctuation

### Rule #2: Use Consistent Header Hierarchy

```markdown
# Main Title (H1)

## Section One (H2)

### Subsection 1.1 (H3)

Some content.

### Subsection 1.2 (H3)

More content.

## Section Two (H2)

Content here.
```

**Must follow this pattern:**
- ✅ Increment by exactly 1 level (h1 → h2 → h3, never h1 → h3)
- ✅ Never skip levels
- ✅ Only one h1 in document (the title)
- ✅ Each header surrounded by blank lines
- ✅ Headers start at column 0 (no indentation)

### Rule #3: Format Lists Correctly

**Unordered lists (pick one marker, use consistently):**

```markdown
* Item 1
* Item 2
* Item 3
```

**Nested unordered (3 space indent):**

```markdown
* Item 1
   * Nested item 1.1
   * Nested item 1.2
* Item 2
   * Nested item 2.1
```

**Ordered lists (always use `1.`):**

```markdown
1. First step
1. Second step
1. Third step
```

**Nested ordered (3 space indent):**

```markdown
1. Step 1
   1. Substep 1.1
   1. Substep 1.2
1. Step 2
   1. Substep 2.1
```

**List requirements:**
- ✅ Blank line BEFORE first item
- ✅ Blank line AFTER last item
- ✅ Consistent marker throughout (all `*` or all `-`, never mixed)
- ✅ Exactly 1 space after marker
- ✅ Nested items use 3 spaces (not 2, not 4)
- ✅ No hanging indents unless using multi-paragraph items

### Rule #4: Format Code Blocks

**Fenced code blocks (preferred):**

```markdown
Here's an example:

```csharp
public static string Hello()
{
    return "world";
}
```

More text after.
```

**Requirements:**
- ✅ Blank line before opening fence
- ✅ Blank line after closing fence
- ✅ Always specify language (`` ```python `` not `` ``` ``)
- ✅ Use consistent fence character (always `` ``` ``)
- ✅ Use fenced, not indented code blocks

**Inline code (backticks):**

```markdown
Use the `const` keyword or `let` for variables.
```

**Requirements:**
- ✅ No spaces inside backticks: `` `code` `` not `` ` code ` ``
- ✅ Use for short code snippets only

### Rule #5: Format Links and URLs

**Proper link syntax:**

```markdown
[Link text](https://example.com)
```

**Bare URLs must have angle brackets:**

```markdown
<https://example.com>
```

**NOT valid:**

```markdown
❌ (Link text)[https://example.com]  (reversed syntax)
❌ https://example.com  (bare URL)
❌ [ Link ](url)  (spaces inside brackets)
```

### Rule #6: Format Emphasis

**Bold (no spaces inside):**

```markdown
This is **bold text**.
```

**Italic (no spaces inside):**

```markdown
This is *italic text*.
```

**NOT valid:**

```markdown
❌ This is ** bold ** text  (spaces inside)
❌ This is * italic * text  (spaces inside)
```

### Rule #7: Use Headers, Not Bold, for Sections

**Correct:**

```markdown
## Important Section

Content goes here.
```

**NOT valid:**

```markdown
❌ **Important Section**

Content goes here.
```

### Rule #8: Use Blockquotes Properly

**Correct syntax with continuation:**

```markdown
> This is a blockquote.
>
> This is the same blockquote continued.

Here's text after the blockquote.
```

**NOT valid:**

```markdown
❌ > Quote 1
❌ 
❌ > Quote 2
(This creates two separate quote blocks with potential parser issues)
```

### Rule #9: Use Consistent Horizontal Rules

```markdown
Some content above.

---

Some content below.
```

**Requirements:**
- ✅ Use same symbol throughout (e.g., all `---`)
- ✅ Have blank line before and after
- ✅ Use 3+ consecutive characters

**NOT valid:**

```markdown
❌ ---    (some uses)
❌ ***    (other uses - inconsistent)
```

### Rule #10: File Structure

**Valid file start and end:**

```markdown
# Title

Content...

More content...
[EOF with newline]
```

**Requirements:**
- ✅ Start with `# Title` (h1 header)
- ✅ End with newline character (no text right before [EOF])
- ✅ No characters after final newline
- ✅ All lines <80 characters (except URLs or code)

**NOT valid:**

```markdown
❌ This file starts with text, not header
❌ Text[EOF]  (no final newline)
❌ [EOF][EOF]  (multiple final newlines)
```

## Line Length Guidelines

| Guideline | Max Length | Exception |
|-----------|-----------|-----------|
| **Regular text** | 80 chars | None |
| **Code blocks** | 80 chars | Long URLs or code that can't wrap |
| **Tables** | 80 chars | May exceed for readability |
| **Headers** | 80 chars | None |
| **Lists** | 80 chars | Long URLs in list items |

**Wrapping strategy:**

```markdown
This is a very long line that exceeds the 80 character limit and needs to be
wrapped to meet markdown best practices for readability and consistency with
lint rules.
```

## Common Mistakes & Fixes

| Mistake | Impact | Fix |
|---------|--------|-----|
| **Mix list markers** (✗ `*` then `-`) | Linting error (MD004) | Use only one marker type |
| **Missing blank line before list** | Linting error (MD032), parser confusion | Add blank line before first `*` |
| **No language in code block** | Linting error (MD040), poor readability | Add language: `` ```csharp `` |
| **Trailing spaces** | Linting error (MD009) | Delete spaces at line end |
| **Hard tabs for indent** | Linting error (MD010) | Replace tabs with spaces |
| **Skipped header levels** | Linting error (MD001) | Increment by exactly 1 level |
| **Multiple h1 headers** | Linting error (MD025) | Only one `#`, rest are `##` or `###` |
| **Header without blank line before** | Linting error (MD022), parser failure | Add blank line above header |
| **Spaces inside `**bold**`** | Linting error (MD037) | Remove: `**bold**` not `** bold **` |
| **Bare URL** | Linting error (MD034), no link parsing | Wrap: `<http://example.com>` |
| **No newline at EOF** | Linting error (MD047) | Add newline at very end |
| **Line too long** | Linting error (MD013) | Wrap to ~80 character target |

## Validation Checklist Before Submitting

**Before considering any markdown file complete, verify:**

- [ ] **MD001**: No skipped header levels (h1 → h2 → h3, never h1 → h3)
- [ ] **MD002**: First header is h1 (`# Title`)
- [ ] **MD003**: Header style consistent (all ATX atx style with `#`)
- [ ] **MD004**: Unordered list markers consistent (all `*` or all `-`)
- [ ] **MD005**: List items at same level have same indentation
- [ ] **MD006**: Top-level lists start at column 0 (not indented)
- [ ] **MD007**: Nested list indentation consistent (3 or 4 spaces)
- [ ] **MD009**: No trailing spaces at end of lines
- [ ] **MD010**: No hard tabs (use spaces for indentation)
- [ ] **MD011**: Links have correct syntax `[text](url)` not `(text)[url]`
- [ ] **MD012**: No multiple consecutive blank lines
- [ ] **MD013**: Lines <80 characters (except URLs)
- [ ] **MD014**: No `$` in code blocks unless showing output
- [ ] **MD018**: Space after `#` in headers (`# Header` not `#Header`)
- [ ] **MD019**: Only one space after `#` in headers (`# Header` not `#  Header`)
- [ ] **MD020**: Closed headers have spaces: `# Header #`
- [ ] **MD021**: Only one space in closed headers: `# Header #` not `#  Header  #`
- [ ] **MD022**: Headers surrounded by blank lines (except file edges)
- [ ] **MD023**: Headers start at column 0 (no indentation)
- [ ] **MD024**: Each header has unique text
- [ ] **MD025**: Only one h1 header (top level)
- [ ] **MD026**: Headers end without punctuation (`. , ; : ! ?`)
- [ ] **MD027**: One space after blockquote marker: `> Quote` not `>  Quote`
- [ ] **MD028**: Blockquotes properly separated or continued with `>`
- [ ] **MD029**: Ordered lists use `1.` prefix consistently
- [ ] **MD030**: Exactly 1 space after list markers
- [ ] **MD031**: Code blocks surrounded by blank lines
- [ ] **MD032**: Lists surrounded by blank lines
- [ ] **MD033**: No inline HTML (use markdown)
- [ ] **MD034**: URLs wrapped in angle brackets: `<url>`
- [ ] **MD035**: Horizontal rules consistent (`---` or `***`)
- [ ] **MD036**: Sections use headers, not **bold** text
- [ ] **MD037**: No spaces inside emphasis: `**bold**` not `** bold **`
- [ ] **MD038**: No spaces inside code: `` `code` `` not `` ` code ` ``
- [ ] **MD039**: No spaces inside link text: `[text](url)` not `[ text ](url)`
- [ ] **MD040**: Code blocks have language specified
- [ ] **MD041**: First line is h1 header
- [ ] **MD046**: Code blocks use fenced syntax, not indentation
- [ ] **MD047**: File ends with single newline

## Real-World Examples

### Example 1: Project README

```markdown
# MyProject

## Overview

MyProject is a framework for...

## Installation

To install MyProject:

```powershell
dotnet add package MyProject
```

## Usage

Here's a basic example:

```csharp
var mp = new MyProject();
mp.Start();
```

## Features

* Fast and lightweight
* Easy to use
* Well documented
   * API docs
   * Examples
   * Tutorials

## Contributing

1. Fork the repository
1. Create a feature branch
1. Commit your changes
1. Push to the branch
1. Create a Pull Request

## License

MIT
```

**Validates:**
- ✅ Starts with `# MyProject` (h1)
- ✅ Headers increment by 1 level
- ✅ All headers surrounded by blank lines
- ✅ Code blocks have language
- ✅ Lists use consistent markers
- ✅ Proper list nesting
- ✅ Ends with newline

## References

**Markdownlint Official Documentation:**
- Rules: https://github.com/markdownlint/markdownlint/blob/main/docs/RULES.md
- Style guide: https://cirosantilli.com/markdown-style-guide
- GitHub markdown: https://guides.github.com/features/mastering-markdown/

**Validation Tools:**
- Online: https://www.markdownlint.com/ (paste markdown to validate)
- CLI: `npm install -g markdownlint-cli`
- Pre-commit hook: Configure `.markdownlintrc` in repository root

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/donellmccoy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
