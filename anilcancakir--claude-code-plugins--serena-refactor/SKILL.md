---
name: serena-refactor
description: | Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Serena Safe Refactoring

## Cross-Codebase Rename

```
rename_symbol(old_name="calcTax", new_name="calculate_tax")
```
- Updates ALL references automatically
- Works across files
- Handles imports/exports

## Replace Function Body

```
replace_symbol_body(symbol_name="process_payment", new_body="...")
```
- Preserves function signature
- Safer than text replacement
- Maintains formatting

## Pre-Refactoring Checklist

1. **Check impact**:
   ```
   find_referencing_symbols("function_to_change")
   ```
   Review all affected locations before changing.

2. **Verify tests exist**:
   ```
   find_symbol("test_function_to_change")
   ```

3. **Run tests after**:
   ```bash
   pytest tests/test_module.py -v
   ```

## Common Patterns

### Rename Function
```
find_referencing_symbols("old_name")  # Check impact
rename_symbol(old_name="old_name", new_name="new_name")
# Run tests
```

### Replace Implementation
```
find_symbol("function_name", include_body=true)  # See current
replace_symbol_body("function_name", new_body="...")
# Run tests
```

## Safety Notes

- Always check references before renaming
- Run tests after each refactoring step
- Document patterns in Serena memory for future reference

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
