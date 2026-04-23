---
name: api-docs-reviewer
description: Audit public API documentation for consistency, accuracy, and completeness. Use when reviewing library docs, auditing module documentation, checking that all public functions are documented, verifying doc comments match code behavior, or ensuring API docs have usage examples. Triggers on "review docs", "audit documentation", "check API docs", or documentation quality concerns. Use when this capability is needed.
metadata:
  author: jtdowney
---

# API Documentation Reviewer

Systematic audit of public API documentation for quality and completeness.

## Audit Workflow

### 1. Scope Discovery

Identify all public API surface:
- Exported modules, functions, types, and constants
- Entry points and main interfaces
- Language-specific patterns (e.g., Gleam `pub`, Rust `pub`, Python `__all__`)

### 2. Completeness Check

For each public item, verify:

| Item | Required | Recommended |
|------|----------|-------------|
| Module | Purpose statement | Usage overview |
| Function | What it does | Parameters, return, example |
| Type/Struct | Definition purpose | Field descriptions |
| Constant | What it represents | When to use |

Flag undocumented public items.

### 3. Accuracy Verification

Compare docs against implementation:
- Parameter names match function signature
- Return type descriptions match actual returns
- Stated behavior matches code logic
- Error conditions documented match actual error cases
- Examples compile/run correctly

### 4. Consistency Review

Check uniform style across the codebase:
- Terminology (same concept = same word everywhere)
- Formatting (code blocks, emphasis, lists)
- Sentence structure (imperative vs declarative)
- Parameter documentation order
- Example code style

### 5. Example Coverage

Prioritize examples for:
- Main entry points
- Complex functions with multiple parameters
- Functions with non-obvious behavior
- Error handling patterns

## Output Format

Produce a structured report:

```markdown
## Documentation Audit: [Module/Project]

### Summary
- Total public items: X
- Documented: Y (Z%)
- Missing docs: N
- Accuracy issues: N
- Consistency issues: N

### Missing Documentation
- [ ] `function_name` - no doc comment
- [ ] `TypeName` - undocumented

### Accuracy Issues
- `function_name`: Parameter `foo` documented as "X" but actually does "Y"
- `other_func`: Return type says `Result` but returns `Option`

### Consistency Issues
- Mixed terminology: "error"/"failure"/"exception" used interchangeably
- Inconsistent example style: some use `let x =`, others use `x :=`

### Recommendations
1. Priority fixes (accuracy)
2. Missing docs for key entry points
3. Style guide suggestions
```

## Language-Specific Patterns

### Gleam
- Function docs: triple slash comments (`///`)
- Module docs: quadruple slash comments (`////`)
- All `pub` items should have doc comments

### Rust
- Item docs: triple-slash comments
- Module docs: inner doc comments (//!)
- Exclude items marked with doc(hidden) attribute
- Verify doctests compile

### Python
- Docstrings on modules, classes, functions
- Check `__all__` exports documented
- Verify type hints match docstring types

### Go
- Comment before declaration
- Package comment in one file
- Exported names (capitalized) documented

### TypeScript/JavaScript
- JSDoc comments
- Check exported items from `index.ts`
- Verify `@param`, `@returns` accuracy

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jtdowney) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
