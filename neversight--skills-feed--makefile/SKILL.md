---
name: makefile
description: Guidelines when dealing with Makefiles Use when this capability is needed.
metadata:
  author: neversight
---

# Makefile Formatting

## Instructions

When writing or modifying Makefiles, follow these formatting guidelines:

1. Each target should have a `.PHONY` declaration
2. Format should follow this pattern:
   - `.PHONY: target-name` on its own line
   - `target-name:` on the next line
   - Commands indented with a tab
   - Empty line between targets

## Example

```makefile
.PHONY: foo
foo:
	@echo 'foo'

.PHONY: bar
bar:
	@echo 'bar'
```

## When to Use

Apply these guidelines when:
- Creating new Makefile targets
- Modifying existing Makefiles
- Reviewing Makefile code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
