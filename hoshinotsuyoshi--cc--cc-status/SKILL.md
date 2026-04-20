---
name: cc-status
description: Check Claude Code usage status Use when this capability is needed.
metadata:
  author: hoshinotsuyoshi
---

# Claude Code Usage Status Check

Display current Claude Code usage status. Use for weekly retrospectives.

## Tasks

1. Check "Lessons Learned" and "Things to Try" in CLAUDE.md
2. Check records in docs/claude-code-routine.md
3. Display progress summary

## Output Format

```
## Usage Status Summary

### Lessons Learned (Practiced)
- ...

### Things to Try (Not Started)
| Item | Difficulty | Next Action |
|------|------------|-------------|
| ... | Easy/Needs Investigation | ... |

### Recent Records
- Show recent records from docs/claude-code-routine.md

## Recommended Action This Week

Pick just one to propose:
- What
- Why now
- How
```

## Notes

- Only show status. Changes are done with `/cc-improve`
- Purpose is simple status check

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoshinotsuyoshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
