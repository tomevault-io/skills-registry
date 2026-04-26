---
name: git
description: Git operations with intelligent commit messages and branch management Use when this capability is needed.
metadata:
  author: nicolasestrem
---

# Git



# /git - Git Operations

## Purpose
Execute Git operations with intelligent commit messages, branch management, and workflow optimization.

## Usage
```
/git [operation] [args] [--smart-commit] [--branch-strategy]
```

## Arguments
- `operation` - Git operation (add, commit, push, pull, merge, branch, status)
- `args` - Operation-specific arguments
- `--smart-commit` - Generate intelligent commit messages
- `--branch-strategy` - Apply branch naming conventions
- `--interactive` - Interactive mode for complex operations

## Execution
1. Analyze current Git state and repository context
2. Execute requested Git operations with validation
3. Apply intelligent commit message generation
4. Handle merge conflicts and branch management
5. Provide clear feedback and next steps

## Claude Code Integration
- Uses Bash for Git command execution
- Leverages Read for repository analysis
- Applies TodoWrite for operation tracking
- Maintains Git best practices and conventions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nicolasestrem) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
