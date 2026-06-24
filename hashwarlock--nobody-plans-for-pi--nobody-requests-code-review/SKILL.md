---
name: nobody-requests-code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
metadata:
  author: hashwarlock
---

# Requesting Code Review

## Overview

Before declaring work complete or merging, run a structured self-review or delegate to the reviewer subagent.

**Announce at start:** "I'm using the requesting-code-review skill to verify this work."

## Pre-Review Checklist

Before requesting review, verify:

- [ ] All tests pass (run them, don't assume)
- [ ] No linting errors
- [ ] Changes match the plan/spec
- [ ] No debug code, commented-out code, or TODOs left behind
- [ ] No unrelated changes mixed in
- [ ] Commit messages are clear and descriptive
- [ ] Edge cases handled

## Self-Review Process

1. **Read the diff** — `git diff main...HEAD` (or appropriate base)
2. **Check each file** — Does every change serve the goal?
3. **Look for** —
   - Hardcoded values that should be config
   - Missing error handling
   - Security issues (injection, auth bypass, secrets)
   - Performance issues (N+1 queries, unbounded loops)
   - Missing tests for new code paths
4. **Verify requirements** — Re-read spec, check each requirement is met

## Delegated Review

Use the reviewer subagent for an independent perspective:

```
/review <scope of changes>
```

Or via the subagent tool directly:
```
Use reviewer agent to review: <description of what changed>
```

## Severity Levels

| Level | Action | Example |
|-------|--------|---------|
| **Critical** | Must fix before merge | Security hole, data loss, crash |
| **Warning** | Should fix | Missing validation, poor error message |
| **Suggestion** | Consider | Better naming, minor refactor |

## Red Flags

- Skipping review because "it's a small change"
- Reviewing your own code without a checklist
- Merging without running the full test suite

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
