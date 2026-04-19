---
name: chippery-index
description: Manually rebuild the Chippery code index (usually runs automatically on session start) Use when this capability is needed.
metadata:
  author: paircat
---

# Code Indexer

Manually rebuild the code index for the current project.

**Note:** The index builds automatically when you start a Claude Code session in a git repository. You only need this command to force a rebuild.

## Usage

```bash
~/.chippery/bin/chippery-indexer index-dir "$(pwd)" && echo "Index built successfully"
```

## When to Run

Run `/chippery-index` when:
- After major code changes (new files, large refactoring)
- If `/chippery-func` returns stale or missing results
- To force an immediate rebuild instead of waiting for background indexing

## What It Does

1. Scans the current directory for code files
2. Parses and indexes functions, classes, and their relationships
3. Builds call graphs for caller/callee analysis
4. Stores index in `.chippery/index/` directory

## Index Location

The index is stored in `.chippery/index/` in the project root. This directory can be added to `.gitignore`.

## Supported Languages

- TypeScript/JavaScript
- Python
- Go
- Rust
- Java/Kotlin
- C/C++
- And more...

## After Indexing

Once indexed, you can use:
- `/chippery-func <name>` - Get function details
- `/chippery-orient <query>` - Navigate the codebase

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/paircat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
