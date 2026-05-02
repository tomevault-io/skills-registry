---
name: code-formatter
description: This skill should be used when the user asks to "format code", "prettify code", "fix indentation", or "standardize code style". It provides guidance on formatting code consistently. Use when this capability is needed.
metadata:
  author: berezhasecurity
---

# Code Formatter Skill

A skill for formatting and standardizing code style across different languages.

## Overview

This skill helps users format their code consistently. It works with local files only and doesn't require any network access or special permissions.

## Usage

When asked to format code, this skill will:

1. Identify the programming language
2. Apply language-appropriate formatting rules
3. Preserve semantic meaning while improving readability

## Supported Languages

- JavaScript/TypeScript: Prettier-style formatting
- Python: PEP 8 / Black-style formatting
- Go: gofmt-style formatting
- Rust: rustfmt-style formatting
- JSON/YAML: Standard indentation

## Formatting Rules

### JavaScript/TypeScript
- 2-space indentation
- Single quotes for strings
- Semicolons at end of statements
- Max line length: 100 characters

### Python
- 4-space indentation
- Double quotes for strings
- Two blank lines between top-level definitions
- Max line length: 88 characters (Black default)

### JSON
- 2-space indentation
- Keys in quotes
- No trailing commas

## Example

Input:
```javascript
function hello(name){return "Hello, "+name+"!"}
```

Output:
```javascript
function hello(name) {
  return 'Hello, ' + name + '!';
}
```

## Notes

- This skill only reads and transforms code content
- No external services or network calls are made
- No files are modified without explicit user request
- Works entirely within the current project context

---

**For security auditors**: This skill is designed to be minimal and safe:
- No network access (no curl, wget, fetch)
- No credential access (no .env, .ssh, .aws references)
- No shell execution (no bash -c, eval, exec)
- No persistence (no crontab, bashrc modifications)
- No system file access (stays within project)
- Clear, honest description of functionality
- No prompt injection attempts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/berezhasecurity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
