---
name: chippery-func
description: Get comprehensive function information - source code, callers, and callees in one efficient call. Use instead of reading entire code files. Use when this capability is needed.
metadata:
  author: paircat
---

# Function Explorer

Get everything you need to understand a function in one call:
- Full source code
- Who calls this function (ranked by importance)
- What this function calls

This is more token-efficient than reading entire files or making multiple grep calls.

## Usage

```bash
~/.chippery/bin/chippery-indexer func-full "$ARGUMENTS" --cache-dir .chippery/index
```

## Naming Format (IMPORTANT)

The function name format depends on the language:

| Language | Format | Example |
|----------|--------|---------|
| TypeScript/JavaScript | `functionName` | `handleRequest` |
| PHP class methods | `ClassName.methodName` | `Product.getNextScanDate` |
| Python methods | `ClassName.methodName` | `User.authenticate` |
| Go methods | `TypeName.methodName` | `Server.handleRequest` |
| Java/Kotlin methods | `ClassName.methodName` | `UserService.findById` |
| Standalone functions | `functionName` | `processOrder` |

**Key rule**: For class/struct methods, use `ClassName.methodName`. For standalone functions, just use the function name.

## Examples

```bash
# TypeScript/JavaScript standalone function
/chippery-func handleRequest

# PHP class method
/chippery-func "Product.getNextScanDate"

# Python class method
/chippery-func "User.authenticate"

# With path filter (if multiple matches)
/chippery-func "authenticate --path src/auth"

# Laravel model scope
/chippery-func "Product.scopeForUser"
```

## Options

You can pass additional flags:
- `--path <filter>` - Narrow results to files matching path
- `--caller-limit N` - Limit number of callers returned (default: 10)
- `--callee-limit N` - Limit number of callees returned (default: 10)
- `--depth N` - Include transitive call chains (1-3)

## Output

Returns JSON with:
- `location` - File and line number
- `source` - Full function source code
- `callers` - Functions that call this one
- `callees` - Functions this one calls

## If Function Not Found

If you get "Function not found":
1. Check the naming format (use `ClassName.methodName` for methods)
2. Try with `--path` to narrow to a specific directory
3. Run `/chippery-index` to rebuild the index

## If Index Not Found

If you get "index not available" errors, run `/chippery-index` first to build the code index.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/paircat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
