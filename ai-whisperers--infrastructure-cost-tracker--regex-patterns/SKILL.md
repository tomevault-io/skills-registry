---
name: regex-patterns
description: Common regex patterns and validation. Use when writing regular expressions for validation, parsing, text processing, or pattern matching. Use when this capability is needed.
metadata:
  author: ai-whisperers
---

# Regex Patterns Skill

Common regular expression patterns for validation, parsing, text processing, and pattern matching.

## When to Use

- Validating input formats
- Extracting data from text
- Searching and replacing text
- Parsing log files
- Data transformation

## Validation Patterns

### Email

```regex
^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
```

### Phone Number (International)

```regex
^\+?[1-9]\d{1,14}$
```

### URL

```regex
^(https?:\/\/)?(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)$
```

### IPv4 Address

```regex
^(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$
```

### Credit Card

```regex
^(?:4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14}|6(?:011|5[0-9]{2})[0-9]{12}|3[47][0-9]{13}|3(?:0[0-5]|[68][0-9])[0-9]{11})$
```

## Programming Patterns

### Hex Color

```regex
^#?([a-fA-F0-9]{6}|[a-fA-F0-9]{3})$
```

### UUID

```regex
^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}$
```

### File Extension

```regex
\.([a-zA-Z0-9]+)$
```

### CSS Class Name

```regex
^-?[_a-zA-Z]+[_a-zA-Z0-9-]*$
```

## Text Processing

### HTML Tags

```regex
<[^>]+>
```

### Markdown Links

```regex
\[([^\]]+)\]\(([^)]+)\)
```

### Whitespace

```regex
\s+
```

### Leading/Trailing Whitespace

```regex
^\s+|\s+$
```

### Multiple Spaces

```regex
{2,}
```

## Log Parsing

### Timestamp (ISO 8601)

```regex
\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}(?:\.\d+)?(?:Z|[+-]\d{2}:\d{2})?
```

### IP in Log

```regex
\b(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b
```

### Error Level

```regex
\b(ERROR|WARN|INFO|DEBUG|TRACE)\b
```

## Python Examples

```python
import re

# Validate email
if re.match(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$', email):
    print("Valid email")

# Extract all URLs
text = "Visit https://example.com or http://test.org"
urls = re.findall(r'https?://[^\s<>"{}|\\^`[\]]+', text)

# Replace multiple spaces
clean_text = re.sub(r'\s+', ' ', text)

# Parse log lines
log_pattern = r'(\d{4}-\d{2}-\d{2}) (\d{2}:\d{2}:\d{2}) (\w+) (.+)'
match = re.match(log_pattern, log_line)
if match:
    date, time, level, message = match.groups()
```

## JavaScript Examples

```javascript
// Validate email
const emailRegex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
if (emailRegex.test(email)) {
    console.log("Valid email");
}

// Extract hashtags
const text = "Love #JavaScript and #Python!";
const hashtags = text.match(/#[a-zA-Z0-9_]+/g);

// Format phone number
const phone = "1234567890";
const formatted = phone.replace(/(\d{3})(\d{3})(\d{4})/, "($1) $2-$3");
```

## Testing Tools

- regex101.com - Interactive regex tester
- regextester.com - JavaScript regex tester
- regexr.com - Regex tester with explanations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-whisperers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
