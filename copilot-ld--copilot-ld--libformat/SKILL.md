---
name: libformat
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libformat Skill

## When to Use

- Rendering markdown as HTML in web applications
- Formatting CLI output with colors and styling
- Converting agent responses for display
- Sanitizing user-generated markdown content

## Key Concepts

**toHtml**: Converts markdown to HTML with sanitization to prevent XSS attacks.

**toTerminal**: Converts markdown to terminal output with ANSI escape codes for
colors and formatting.

## Usage Patterns

### Pattern 1: Web rendering

```javascript
import { toHtml } from "@copilot-ld/libformat";

const html = toHtml("# Hello **World**");
// Returns: <h1>Hello <strong>World</strong></h1>
```

### Pattern 2: Terminal output

```javascript
import { toTerminal } from "@copilot-ld/libformat";

const output = toTerminal("# Hello **World**");
console.log(output); // Colored terminal output
```

## Integration

Used by Web extension to format agent responses. Used by CLI tools for terminal
output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
