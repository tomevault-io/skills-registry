---
name: code-generator
description: Code style (functional, oop, procedural) Use when this capability is needed.
metadata:
  author: zscole
---

# Code Generator Skill

This skill helps generate boilerplate code and scaffolding for various programming languages.

## When to Use

Use this skill when the user asks to:
- Generate a function, class, or module
- Scaffold a new project structure
- Create boilerplate code
- Write code from a description

## Supported Languages

- TypeScript/JavaScript
- Python
- Rust
- Go
- Shell/Bash

## Usage Examples

When the user says "generate a typescript function to validate email addresses", execute the generate script:

```
generate --language typescript --description "validate email addresses"
```

When asked to "scaffold a python CLI tool", use:

```
generate --language python --description "CLI tool scaffold" --style procedural
```

## Best Practices

1. Always confirm the target language with the user if ambiguous
2. Ask clarifying questions about edge cases
3. Include error handling in generated code
4. Add comments explaining the logic
5. Follow language-specific conventions and idioms

## Output Format

The script outputs generated code to stdout. Always present this code in a properly formatted code block with the appropriate language tag.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zscole) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
