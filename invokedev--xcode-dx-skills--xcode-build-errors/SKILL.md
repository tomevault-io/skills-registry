---
name: xcode-build-errors
description: Analyzes Xcode build failures to explain errors and suggest fixes. Use when build fails, compiler errors appear, linker errors occur, code signing fails, or user asks "why did the build fail?" or "what does this error mean?
metadata:
  author: invokedev
---

# Xcode Build Error Analyzer

Parses and explains Xcode build failures, providing actionable guidance for fixing errors.

## When to Use

- User pastes build output containing errors
- User asks "why did my build fail?"
- User asks "what does this error mean?"
- Build log contains compilation, linking, or signing errors
- User shares `.xcresult` bundle path

## Usage

### From Build Output

If the user pastes build output directly, first filter it to extract errors:

```bash
echo "<pasted-output>" | swift ~/.claude/plugins/xcode-dx-skills/skills/xcode-log-filter/scripts/filter-log.swift
```

### From Log File

```bash
swift ~/.claude/plugins/xcode-dx-skills/skills/xcode-build-errors/scripts/parse-build-errors.swift /path/to/build.log
```

### From xcresult Bundle

```bash
swift ~/.claude/plugins/xcode-dx-skills/skills/xcode-build-errors/scripts/parse-build-errors.swift /path/to/Build.xcresult
```

## Error Categories

The script categorizes errors to help with diagnosis:

| Category | Examples |
|----------|----------|
| `compiler` | Type mismatches, missing imports, syntax errors |
| `linker` | Undefined symbols, duplicate symbols, library not found |
| `signing` | Provisioning profile issues, certificate problems |
| `resource` | Missing assets, storyboard errors, Info.plist issues |
| `swift_type` | Complex type inference failures, protocol conformance |
| `dependency` | SPM resolution failures, framework embedding |

## Analysis Approach

1. **Parse errors** using the script to get structured data
2. **Group by category** to identify patterns (e.g., "all linker errors")
3. **Identify root cause** - often the first error causes subsequent ones
4. **Explain the error** in plain terms
5. **Suggest specific fixes** with code examples when applicable

## Common Error Patterns

### Type Mismatch
```
Cannot convert value of type 'X' to expected argument type 'Y'
```
- Check if types are compatible
- Look for missing type conversions or casts
- Consider if an optional needs unwrapping

### Undefined Symbol
```
Undefined symbol: _OBJC_CLASS_$_SomeClass
```
- Framework not linked - add to "Link Binary With Libraries"
- Missing `-ObjC` linker flag for static libraries
- SPM target not properly declared as dependency

### Signing Issues
```
No signing certificate "iOS Development" found
```
- Certificate not installed in Keychain
- Provisioning profile expired or missing
- Bundle identifier mismatch

### Module Not Found
```
No such module 'SomeFramework'
```
- SPM dependency not resolved - run `swift package resolve`
- Framework search paths incorrect
- Build order issue - dependency not built first

## Output Format

The script outputs JSON with:
- `errors`: Array of errors with file, line, message, category
- `error_groups`: Errors grouped by category
- `root_cause_candidates`: Most likely root cause errors
- `fix_suggestions`: Suggested fixes based on error patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/invokedev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
