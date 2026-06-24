---
name: continuous-learning-v2
description: Pattern extraction and skill promotion from session data. Detects repeated patterns and creates draft skills for user approval. Use when this capability is needed.
metadata:
  author: OkminLee
---

# Continuous Learning v2

Automatically detect repeated patterns across sessions and promote them to learned skills.

## When to Activate

- Automatically on session end (via evaluate.js Stop hook)
- When reviewing draft skills at session start

## How It Works

### Detection (evaluate.js)

At session end, the evaluate hook:

1. Loads `config.json` for pattern detection settings
2. Reads today's session file (`~/.claude/sessions/`)
3. Analyzes git log and session data for patterns:
   - `error_resolution` — same error type fixed 3+ times
   - `user_corrections` — approach changed after user feedback
   - `workarounds` — non-standard solutions applied
   - `debugging_techniques` — repeated debugging patterns
   - `project_specific` — project-unique conventions
4. Creates draft skills in `~/.claude/skills/learned/draft-<name>.md`

### Approval Flow

At next session start, `session-start.js` reports draft skills:

```
[SessionStart] 2 draft skill(s) awaiting approval
[SessionStart]   Draft: draft-error-resolution-2026-03-31.md
```

User decides:
- **Approve**: rename to remove `draft-` prefix, becomes active learned skill
- **Reject**: delete the draft file

### Configuration (config.json)

```json
{
  "min_session_length": 10,
  "extraction_threshold": "medium",
  "auto_approve": false,
  "patterns_to_detect": ["error_resolution", "user_corrections", ...]
}
```

- `extraction_threshold`: `low` (2+), `medium` (3+), `high` (5+) occurrences
- `auto_approve`: always `false` — human gate for quality

---
> Source: [OkminLee/everything-claude-code-ios](https://github.com/OkminLee/everything-claude-code-ios) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-20 -->
