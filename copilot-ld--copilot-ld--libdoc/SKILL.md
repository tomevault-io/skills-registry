---
name: libdoc
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libdoc Skill

## When to Use

- Building static documentation sites from markdown
- Serving documentation locally during development
- Parsing YAML front matter from markdown files
- Generating HTML from markdown with templates

## Key Concepts

**build**: Processes markdown files with front matter, applies Mustache
templates, and outputs static HTML.

**serve**: Local development server with live reload for documentation preview.

**parseFrontMatter**: Extracts YAML metadata from markdown file headers.

## Usage Patterns

### Pattern 1: Build documentation

```javascript
import { build } from "@copilot-ld/libdoc";

await build({
  srcDir: "docs",
  outDir: "public",
  template: "template.html.mustache",
});
```

### Pattern 2: Parse front matter

```javascript
import { parseFrontMatter } from "@copilot-ld/libdoc";

const { data, content } = parseFrontMatter(markdownContent);
console.log(data.title); // From YAML header
```

## Integration

Used by `make docs` to build the documentation site. Output served via static
file hosting.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
