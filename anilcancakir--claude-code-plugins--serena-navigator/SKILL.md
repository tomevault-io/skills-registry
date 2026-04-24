---
name: serena-navigator
description: | Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Serena Semantic Navigation

## Token-Efficient Reading Pattern

Instead of reading entire files:

1. **Get overview first**: `get_symbols_overview(file)` - See all functions/classes
2. **Locate specific symbol**: `find_symbol(name)` - Get exact location
3. **Read targeted section**: `read_file(path, start_line, end_line)` - Only what's needed

This pattern saves 90%+ tokens on large files.

## Tool Selection Guide

| Task | Tool |
|------|------|
| See file structure | `get_symbols_overview(file_path)` |
| Find definition | `find_symbol(name)` |
| Find all usages | `find_referencing_symbols(symbol_name)` |
| Search text/comments | `search_for_pattern(pattern)` |

## Example Workflow: Understanding Code

```
1. get_symbols_overview("src/services/payment.py")
   → See PaymentProcessor class with all methods

2. find_symbol("process_payment")
   → Get exact location and signature

3. find_referencing_symbols("process_payment")
   → Find all call sites

4. read_file("src/services/payment.py", start_line=45, end_line=80)
   → Read only the relevant method
```

## When to Use Text Search

Use `search_for_pattern` for:
- String literals and comments
- Configuration values
- Non-code patterns

## Memory Integration

Check existing project knowledge:
```
list_memories()
read_memory("architecture_overview")
```

Save discoveries:
```
write_memory(key="architecture_overview", content="...")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
