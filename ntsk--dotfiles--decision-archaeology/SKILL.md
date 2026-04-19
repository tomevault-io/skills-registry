---
name: decision-archaeology
description: Investigate why code was written a certain way. Use when tracing decision history through git blame, commit logs, PRs, and issues to understand the reasoning behind code changes. Use when this capability is needed.
metadata:
  author: ntsk
---

# Decision Archaeology Skill

## Rules

Read-only operations only.
For `gh api`: GET requests only. No `-X POST/PUT/PATCH/DELETE` or `-f`/`-F` flags.

## Workflow

1. **Identify code** - Target file, lines, or function
2. **Find commit** - `git blame` → introducing commit
3. **Trace PR/Issue** - Check commit message for `#123` references
4. **Summarize rationale** - Output findings

For detailed commands: See [references/commands.md](references/commands.md)

## Key Commands

```bash
git blame -L <start>,<end> <file>    # Find who changed lines
git log -S "code" --oneline          # Find when code was added
gh pr list --search "<sha>" --state all  # Find PR for commit
```

## Output Format

```
## Decision Archaeology Report

### Target
- File: [path/to/file]
- Lines: [XX-YY]

### Timeline

#### [Date] Commit: [short-sha]
- Author: [name]
- Message: [commit message]
- PR: #[number] [title]
- Related Issue: #[number]

### Decision Rationale
[Summary of why, based on PR discussions, issue context, commit messages]

### Key References
- PR #[number]: [URL]
- Issue #[number]: [URL]
- Commit: [sha]
```

## Tips

- Start with `git blame` to find introducing commit
- Check commit message for `#123` references
- PR descriptions and review comments contain the "why"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntsk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
