---
name: markdown
description: Markdown style guide. Use when writing or editing markdown files to ensure consistent formatting with Setext headings, hyphen bullets, and plain-text readability. Use when this capability is needed.
metadata:
  author: jbenner-radham
---

Markdown Style
==============

Format for plain-text readability. Style preferences only; assumes Markdown knowledge.

Headings
--------

Use Setext-style for level 1 and 2 headings:

```markdown
Document Title
==============

Section Heading
---------------
```

Use ATX-style for level 3 and deeper:

```markdown
### Subsection

#### Sub-subsection
```

Lists
-----

Use a hyphen ("-") for bullet list markers.

Emphasis
--------

Bold with asterisks (e.g., `**bold**`). Italicize with underscores (e.g., `_italic_`).

Code
____

All instances of code or shell session examples should go into fenced code blocks.

```typescript
console.log('Hello world!');
```

```sh-session
git diff
```

Always include an info string with a fenced code block and use long form names over abbreviations.

````markdown
<!-- Good -->
```typescript
console.log('Hello, world!');
```

<!-- Bad (no info string) -->
```
console.log('Hello, world!');
```

<!-- Bad (abbreviated info string) -->
```ts
console.log('Hello, world!');
```
````

Spacing
-------

- One blank line between block elements (e.g., headings, paragraphs, block quotations, lists, headings, rules, code blocks, etc.).
- No trailing whitespace.
- Single newline at end of file.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbenner-radham) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
