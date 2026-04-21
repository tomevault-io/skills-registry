---
name: review
description: Focus on UI accessibility Use when this capability is needed.
metadata:
  author: noin-ai
---

# Code Review Skill

Trigger thorough code review using `noin-ai:reviewer` agent.

## Usage

```bash
/review                    # Review staged changes
/review <file>             # Review specific file
/review <directory>        # Review directory
/review --quick            # Critical issues only
/review --security         # Security-focused
/review --accessibility    # UI accessibility
```

## Review Types

| Flag | Focus |
|------|-------|
| (default) | Full review: correctness, quality, performance |
| `--quick` | Critical issues only |
| `--security` | OWASP Top 10, auth flows, input validation |
| `--accessibility` | WCAG 2.1 AA, ARIA, keyboard nav |

## Workflow

```
/review [target] [flags]
    ↓
Resolve target (file/dir/staged)
    ↓
Task(noin-ai:reviewer, review_type, target)
    ↓
Return structured feedback
```

## Output Format

```markdown
## Code Review Summary

**Status**: APPROVE | REQUEST_CHANGES
**Files Reviewed**: X files
**Issues Found**: Y total

### Critical Issues (Must Fix)
1. **[BUG]** `file:line` - Issue description

### Warnings (Should Fix)
1. **[QUALITY]** `file:line` - Concern

### Suggestions (Nice to Have)
1. Consider adding...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/noin-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
