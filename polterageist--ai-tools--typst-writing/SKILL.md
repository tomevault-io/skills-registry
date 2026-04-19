---
name: typst-writing
description: Guidelines for writing documents with Typst, including templates usage and best practices. Use when this capability is needed.
metadata:
  author: polterageist
---

# Typst Writing

## Templates

Use templates from `utils/typst-templates/`:
- `paper.typ` - academic papers
- `blog.typ` - blog posts

## Usage

```typst
#import "../../utils/typst-templates/paper.typ": *

#show: project.with(
  title: "Title",
  authors: ((name: "Author", email: "email@example.com"),),
  lang: "en",
)

= Introduction
...
```

## Best Practices

- Keep sections focused
- Use theorem/definition environments for formal content
- Include TL;DR for long posts
- Compile frequently: `typst compile` or `typst watch`

## Language Support

Set `lang` parameter: `"en"` (default) or `"ru"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/polterageist) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
