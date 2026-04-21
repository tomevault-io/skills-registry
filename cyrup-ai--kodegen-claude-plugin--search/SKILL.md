---
name: search
description: > Use when this capability is needed.
metadata:
  author: cyrup-ai
---

# fs_search Mastery Guide

## Core Concept

`mcp__plugin_kg_kodegen__fs_search` is built on ripgrep - the fastest code search tool available (10-100x faster than grep). It automatically respects .gitignore, supports regex and literal search, handles multiline patterns, and can run searches in the background.

## Two Independent Controls

### 1. WHERE to Search (`search_in`)

- **"content"** (default): Search inside file contents
  - Use for: Finding function definitions, error messages, TODO comments
  - Example: `{search_in: "content", pattern: "fn getUserData"}`

- **"filenames"**: Search file names/paths only
  - Use for: Finding specific files, locating by extension
  - Example: `{search_in: "filenames", pattern: "package.json"}`

### 2. WHAT to Return (`return_only`)

- **"matches"** (default): Full details with line numbers and matched content
  - Use when: You need to see the actual code/text
  - Returns: File path, line number, matched line

- **"paths"**: Just unique file paths (like rg -l)
  - Use when: You only need to know WHICH files contain the pattern
  - Returns: List of file paths only

- **"counts"**: Match counts per file (like rg -c)
  - Use when: You need statistics (e.g., "how many TODOs per file")
  - Returns: File path and count

**These combine independently:** any `search_in` works with any `return_only`.

## Decision Tree

### Finding Specific Files
```
User: "find package.json" or "locate Cargo.toml" or "which files are *.rs"

Use:
{
  "search_in": "filenames",
  "pattern": "package.json"
}

{
  "search_in": "filenames",
  "pattern": "\\.rs$"
}
```

### Finding Code Patterns
```
User: "where is getUserData function" or "find TODO comments"

Use:
{
  "search_in": "content",
  "pattern": "fn getUserData"
}

{
  "search_in": "content",
  "pattern": "TODO"
}
```

### Getting File Lists Only
```
User: "which files have errors" or "list files with console.log"

Use:
{
  "search_in": "content",
  "pattern": "error",
  "return_only": "paths"
}

{
  "search_in": "content",
  "pattern": "console\\.log",
  "return_only": "paths"
}
```

### Counting Matches
```
User: "how many TODOs per file" or "count import statements"

Use:
{
  "search_in": "content",
  "pattern": "TODO",
  "return_only": "counts"
}

{
  "search_in": "content",
  "pattern": "^import",
  "return_only": "counts"
}
```

## Critical Parameters

### literal_search (default: false)

Set to `true` when searching for code with special regex characters:

```json
// ❌ WRONG - regex interprets . and () as special chars
{"pattern": "toast.error('test')"}

// ✅ CORRECT - literal string match
{"pattern": "toast.error('test')", "literal_search": true}

// ❌ WRONG - brackets are regex character class
{"pattern": "array[0]"}

// ✅ CORRECT - literal bracket match
{"pattern": "array[0]", "literal_search": true}
```

**Rule:** If searching for actual code symbols like `.`, `(`, `)`, `[`, `]`, `{`, `}`, use `literal_search: true`.

### boundary_mode

Controls pattern matching boundaries:

- `null` (default): Match anywhere (substring)
  - `"test"` matches in `testing`, `attest`, `test()`

- `"word"`: Match whole words only (uses `\b` anchors)
  - `"test"` matches `test()` but NOT `testing` or `attest`

- `"line"`: Match complete lines only (uses `^$` anchors)
  - Pattern must match the entire line

```json
// Match whole word "test" (not "testing")
{
  "pattern": "test",
  "boundary_mode": "word"
}
```

### case_mode (default: "sensitive")

- `"sensitive"`: Exact case matching
- `"insensitive"`: Case-insensitive search
- `"smart"`: Insensitive if pattern is all lowercase, sensitive otherwise

```json
// Case-insensitive search
{
  "pattern": "error",
  "case_mode": "insensitive"
}
// Matches: error, Error, ERROR, ErRoR
```

### file_pattern

Filter files by glob pattern:

```json
// Only JavaScript and TypeScript
{"file_pattern": "*.{js,ts}"}

// Only Rust files
{"file_pattern": "*.rs"}

// Only Python test files
{"file_pattern": "test_*.py"}
```

### type / type_not

Use ripgrep's built-in file type definitions:

```json
// Only Rust and Python files
{"type": ["rust", "python"]}

// Exclude tests and JSON
{"type_not": ["test", "json"]}
```

Common types: `rust`, `python`, `javascript`, `typescript`, `json`, `markdown`, `yaml`, `toml`

### pattern_mode (Filename Search Only)

**IMPORTANT**: This parameter ONLY works with `search_in: "filenames"`. It has NO effect on content searches.

Control how filename patterns are interpreted:

```json
// Auto-detection (default)
{
  "search_in": "filenames",
  "pattern": "*.md"
}
// → Auto-detected as Glob, matches: README.md, NOTES.md

// Force regex interpretation
{
  "search_in": "filenames",
  "pattern": "test.*",
  "pattern_mode": "regex"
}
// → Regex mode, matches: test.js, test123.md, testing.txt

// Force substring (literal match)
{
  "search_in": "filenames",
  "pattern": "*.md",
  "pattern_mode": "substring"
}
// → Substring mode, matches ONLY files literally named "*.md"

// Force glob interpretation
{
  "search_in": "filenames",
  "pattern": "lib",
  "pattern_mode": "glob"
}
// → Glob mode, uses shell-style matching
```

**Auto-Detection Priority**: Regex > Glob > Substring

**Detection Rules**:
- **Regex detected** when pattern contains: `^`, `$`, `\.`, `\d`, `\w`, `.*`, `.+`, `|`, `[...]+`, `{n,m}`
- **Glob detected** when pattern contains: `*`, `?`, `**`, `{a,b}`, `[abc]` (without regex markers)
- **Substring** (default): No special characters detected

**Common Use Cases**:
```json
// Find markdown files (auto-detected as glob)
{"search_in": "filenames", "pattern": "*.md"}
// → pattern_type: "glob"

// Find files starting with "test" (auto-detected as regex)
{"search_in": "filenames", "pattern": "^test"}
// → pattern_type: "regex"

// Find files containing "config" (auto-detected as substring)
{"search_in": "filenames", "pattern": "config"}
// → pattern_type: "substring"

// Override: match literal "*.md" filename
{"search_in": "filenames", "pattern": "*.md", "pattern_mode": "substring"}
// → pattern_type: "substring", matches file literally named "*.md"
```

**Output Field: pattern_type**

Every filename search response includes `pattern_type` showing how the pattern was interpreted:

```json
{
  "pattern": "*.rs",
  "pattern_type": "glob",
  "search_in": "filenames",
  "results": [...]
}
```

This helps you understand:
- Whether auto-detection worked as expected
- Why certain files matched or didn't match
- If you need to override with `pattern_mode`

## Performance Optimization

### For Large Codebases

Combine these for 10-100x speedup:

```json
{
  "path": "/large-monorepo",
  "pattern": "config",
  "max_depth": 3,
  "max_filesize": 1048576,
  "type_not": ["json", "lock"]
}
```

**Why this works:**
- `max_depth: 3-4` avoids deep dependency trees (node_modules, vendor, target)
- `max_filesize: 1048576` (1MB) skips huge generated files (minified bundles, lock files)
- `type` or `file_pattern` reduces the number of files to search

**Performance gains:**
- `max_depth` alone: 10-25x faster
- `max_filesize` alone: 10-30x faster
- Combined: 100x+ faster on large monorepos

### Background Execution

For searches that might take a while:

```json
{
  "path": "/huge-repo",
  "pattern": "deprecated",
  "await_completion_ms": 10000,
  "search": 0
}
```

**Behavior:**
- Waits up to 10 seconds for results
- If timeout occurs: returns current partial results, search continues in background
- Check progress later: `{action: "READ", search: 0}`

## Multiline Patterns

Enable with `multiline: true` to search across lines:

```json
{
  "pattern": "struct Config \\{[\\s\\S]*?version",
  "multiline": true
}
```

Matches:
```rust
struct Config {
    port: u16,
    version: String,
}
```

**Use cases:**
- Finding struct/class definitions with specific fields
- Matching function signatures across multiple lines
- Finding multi-line comments or documentation

## Advanced: only_matching

Extract specific parts instead of whole lines:

```json
{
  "pattern": "https?://[^\\s]+",
  "only_matching": true
}
```

Returns just the URLs, not the entire lines containing them.

**Perfect for extracting:**
- URLs from markdown or code
- Version numbers from package files
- Email addresses from documentation
- Function names from code

## Common Search Patterns

### Find Function Definitions (Rust)
```json
{
  "pattern": "fn \\w+\\(",
  "search_in": "content",
  "file_pattern": "*.rs"
}
```

### Find TODO/FIXME Comments
```json
{
  "pattern": "(TODO|FIXME)",
  "search_in": "content",
  "return_only": "paths"
}
```

### Find Files by Extension
```json
{
  "pattern": "\\.tsx?$",
  "search_in": "filenames"
}
```

### Count Imports Per File
```json
{
  "pattern": "^import",
  "return_only": "counts"
}
```

### Find Exact Code (with special chars)
```json
{
  "pattern": "if (user?.role === 'admin')",
  "literal_search": true
}
```

### Whole Word Search
```json
{
  "pattern": "test",
  "boundary_mode": "word"
}
```

### Find Error Handling Patterns
```json
{
  "pattern": "(Error|panic!|unwrap|expect)",
  "file_pattern": "*.rs"
}
```

### Find API Routes
```json
{
  "pattern": "(GET|POST|PUT|DELETE).*\\/api\\/",
  "search_in": "content"
}
```

## When to Use What

| Goal | Tool | Why |
|------|------|-----|
| Find code pattern | fs_search (content) | Fast, regex support, respects .gitignore |
| Find specific file | fs_search (filenames) | Faster than glob when path unknown |
| List directory | fs_list_directory | Simple, one-level listing |
| Read known file | fs_read_file | Direct access, supports offset/length |
| Search + read | fs_search → fs_read_multiple_files | Parallel reads after discovery |

## Troubleshooting

### No Results Found

1. **Check regex special characters** → use `literal_search: true`
2. **Check case sensitivity** → try `case_mode: "insensitive"`
3. **Check file filtering** → verify `file_pattern` or `type` isn't too restrictive
4. **Check .gitignore** → use `no_ignore: true` to override

### Search Too Slow

1. Add `max_depth: 3` to limit directory traversal
2. Add `max_filesize: 1048576` to skip huge files
3. Use `file_pattern` or `type` to filter file types
4. Consider background execution: `await_completion_ms: 0`

### Pattern Not Matching

1. **Escape regex special chars:** `. * + ? [ ] { } ( ) | ^ $`
2. **Or use literal search:** `literal_search: true`
3. **For multiline patterns:** set `multiline: true`
4. **Check boundaries:** adjust `boundary_mode` if needed

### Pattern Not Matching Expected Files

1. **Check pattern_type in response** → See how pattern was interpreted
2. **Override auto-detection** → Use `pattern_mode: "regex"`, `"glob"`, or `"substring"`
3. **Remember**: Only works for `search_in: "filenames"`, not content search
4. **Literal characters**: Use `pattern_mode: "substring"` or `literal_search: true`

## Quick Reference

```json
// Basic content search
{
  "path": "/project",
  "pattern": "getUserData"
}

// Find files by name
{
  "path": "/project",
  "search_in": "filenames",
  "pattern": "config"
}

// Get file list only
{
  "path": "/project",
  "pattern": "TODO",
  "return_only": "paths"
}

// Literal search (exact match)
{
  "path": "/project",
  "pattern": "array[0]",
  "literal_search": true
}

// Performance optimized
{
  "path": "/project",
  "pattern": "error",
  "max_depth": 3,
  "max_filesize": 1048576,
  "type": ["rust"]
}
```

## Remember

- **Default is regex**: Escape special characters or use `literal_search: true`
- **Default searches content**: Use `search_in: "filenames"` for file names
- **Default shows matches**: Use `return_only: "paths"` for file lists only
- **Respects .gitignore**: Use `no_ignore: true` to override
- **10-100x faster than grep**: Your primary search tool
- **Background execution available**: For long-running searches

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cyrup-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
