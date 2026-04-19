---
name: document
description: Update memory bank with recent progress, decisions, and learnings Use when this capability is needed.
metadata:
  author: fpontejos
---

# Document

Update the memory bank with recent progress, decisions, and learnings.
Use during or at end of session to record work completed.

## Usage

`/document`          # Interactive - asks what to include
`/document auto`     # Auto-detect changes and suggest updates
`/document brief`    # Quick timestamp + summary only

Argument provided: "$ARGUMENTS"

---

## Step 0: Parse Arguments

Parse `$ARGUMENTS` to determine mode:
- Empty → Interactive mode
- `auto` → Auto-detection mode
- `brief` → Brief mode (minimal update)

---

## Step 1: Gather Context (All Modes)

### Read Current State

1. `.claude/memorybank/session.md` - Current focus, stated next steps
2. `.claude/memorybank/progress.md` - Previous entries, last update time
3. `.claude/memorybank/overview.md` - Work areas, project patterns
4. `logs/log_index.yaml` - Active session info

### Detect Changes

1. **Git changes** (if git repo):
   ```bash
   git diff --name-only HEAD~1..HEAD  # Recent commits
   git diff --name-only               # Uncommitted changes
   git diff --stat                    # Change summary
   ```

2. **Time since last update**:
   - Parse timestamp from session.md
   - Calculate hours since last `/document`

3. **Session work** (if log active):
   - Read current log file entries
   - Identify tasks logged

---

## Mode: Brief (`/document brief`)

Minimal update - just timestamp and one-line summary.

### Process

1. Get current timestamp
2. Add brief entry to session.md
3. Update timestamp in both files

### Updates

**session.md** - Update only:
```markdown
## Recent Work ([date])

### Completed
- [One-line summary of recent work]

---
*Last updated: [timestamp]*
```

**progress.md** - Append only:
```markdown
### [date] - Brief Update
- [One-line summary]

---
*Last updated: [timestamp]*
```

### Output

```
## Documentation Updated (Brief)

Added brief entry to memory bank:
- session.md: Updated recent work
- progress.md: Added entry

*Timestamp: [timestamp]*
```

---

## Mode: Auto (`/document auto`)

Auto-detect what changed and suggest comprehensive updates.

### Detection Process

1. **Analyze git changes**:
   - Files modified/added/deleted
   - Categorize by work area
   - Identify scope (feat/fix/refactor/docs/test)

2. **Analyze conversation** (current session):
   - Decisions made
   - Problems solved
   - Patterns discovered
   - Questions answered

3. **Compare to session.md**:
   - Which "Next Steps" were completed?
   - Any deviations from plan?
   - New blockers emerged?

4. **Check log entries**:
   - Tasks logged during session
   - Gaps between log and actual work

### Present Suggestions

```
## Auto-Detected Changes

### Files Changed
- [N] files modified in [areas]
- Key changes: [brief summary]

### Completed Tasks
From session.md "Next Steps":
- [x] [task 1] - completed
- [x] [task 2] - completed
- [ ] [task 3] - not done

### Decisions Detected
From conversation:
1. [Decision]: [brief rationale]
2. [Decision]: [brief rationale]

### Patterns/Learnings
- [Pattern discovered]

### Suggested Updates

**progress.md**:
- Add milestone: [description]
- Record decision: [decision]
- Note pattern: [pattern]

**session.md**:
- Update focus to: [new focus]
- Mark completed: [items]
- Add next steps: [new items]

---
Proceed with these updates? [Yes / Edit first / Cancel]
```

### After Confirmation

Apply updates and show summary.

---

## Mode: Interactive (`/document`)

Guided documentation with questions.

### Process

1. **Show current state**:
   ```
   ## Current Memory Bank State

   **Last updated**: [timestamp] ([Xh ago])
   **Current focus**: [from session.md]
   **Active session**: [Yes/No] ([duration])
   ```

2. **Ask what to document** (conversationally):
   - "What work did you complete since the last update?"
   - "Any important decisions made?"
   - "Did you discover any patterns or learnings?"
   - "Any new blockers or resolved blockers?"
   - "What should be the focus going forward?"

3. **Confirm and apply updates**

### Question Flow

**Phase 1: Completed Work**
```
What work was completed? (Brief bullet points)
```

**Phase 2: Decisions** (if applicable)
```
Were any significant decisions made? If so:
- What was decided?
- What was the rationale?
```

**Phase 3: Learnings** (if applicable)
```
Any patterns, learnings, or things to remember for future sessions?
```

**Phase 4: Status Changes**
```
Any changes to:
- Work area status (active/paused/complete)?
- Blockers (new or resolved)?
- Focus area?
```

**Phase 5: Next Steps**
```
What are the immediate next steps?
```

---

## Update Templates

### progress.md Entry Template

```markdown
## Session: [YYYY-MM-DD]

### Completed
- [Work item 1]
- [Work item 2]
- [Work item 3]

### Decisions
| Decision | Rationale | Impact |
|----------|-----------|--------|
| [What was decided] | [Why] | [What it affects] |

### Patterns Discovered
- **[Pattern name]**: [Description and when to apply]

### Deviations from Plan
- [What changed]: [Why it changed]

### Blockers
- **Resolved**: [Blocker that was resolved]
- **New**: [New blocker encountered]

---
*Last updated: [timestamp]*
```

### session.md Update Template

```markdown
# Current Session

## Focus Area
[Current focus - what you're working on]

## Recent Work ([date])

### Completed
1. [Most recent completed item]
2. [Previous completed item]
3. [Earlier completed item]

### In Progress
- [Item currently being worked on]

## Next Steps
- [ ] [Immediate next task]
- [ ] [Following task]
- [ ] [Future task]

## Blockers
[Current blockers or "None"]

## Notes
[Any important notes for next session]

---
*Last updated: [timestamp]*
```

### CLAUDE.md Pattern Template

Only update if significant pattern discovered:

```markdown
## Key Patterns

### [Pattern Name]
**Context**: [When this applies]
**Pattern**: [What to do]
**Example**: [Brief example]
```

---

## What to Document

### Always Document

| Category | Examples |
|----------|----------|
| **Completed work** | Features added, bugs fixed, refactors done |
| **Decisions** | Architecture choices, approach selections |
| **Blockers** | New blockers, resolved blockers |
| **Status changes** | Work area status updates |

### Document If Significant

| Category | Threshold |
|----------|-----------|
| **Patterns** | Reusable insight, not obvious |
| **Learnings** | Would help future sessions |
| **Deviations** | Departed from original plan |

### Don't Document

| Category | Reason |
|----------|--------|
| **Trivial changes** | Typo fixes, formatting |
| **Temporary work** | Experiments that were reverted |
| **Obvious details** | Self-evident from code |

---

## Granularity Guide

### Too Brief (Avoid)
```
- Did some work on auth
```

### Too Detailed (Avoid)
```
- Modified line 47 of auth.py to change the variable name from 'usr' to 'user'
- Then modified line 48 to update the function call
- Also updated the import on line 3
```

### Just Right
```
- Implemented JWT token refresh in auth module
- Added token expiry validation with 5-minute buffer
- Updated auth middleware to handle refresh flow
```

---

## Timestamp Handling

Always get timestamp from system:
```bash
date '+%Y-%m-%d %H:%M:%S %Z'
```

### Timestamp Locations

| File | Location | Format |
|------|----------|--------|
| session.md | Footer | `*Last updated: YYYY-MM-DD HH:MM:SS TZ*` |
| progress.md | Footer + section headers | Same format |
| Log entries | Entry headers | `### YYYY-MM-DD HH:MM:SS TZ` |

---

## Cross-File Consistency

When updating, ensure consistency:

| If you update... | Also check... |
|------------------|---------------|
| Work area status in progress.md | overview.md "Work Areas" table |
| Focus area in session.md | Matches active work area |
| Blockers resolved | Remove from session.md blockers |
| New patterns | Consider adding to CLAUDE.md |

---

## Output Format

After documentation is complete:

```
## Documentation Updated

### Changes Made

**progress.md**:
- Added session entry for [date]
- Recorded [N] completed items
- Documented [N] decisions
- [Added/Updated] [N] patterns

**session.md**:
- Updated focus to: [focus]
- Marked [N] items completed
- Added [N] next steps
- [Updated blockers / No blocker changes]

**CLAUDE.md** (if updated):
- Added pattern: [pattern name]

### Summary
[One-line summary of what was documented]

---
*Documentation timestamp: [timestamp]*

→ Run `/handoff` when ready to commit
```

---

## Error Handling

### No Memory Bank

```
## Documentation Error

Memory bank not found at `.claude/memorybank/`.

Run `/onboard-claude` to initialize the memory bank first.
```

### No Changes Detected (Auto Mode)

```
## No Changes Detected

Auto-detection found no significant changes since last update.

Last documented: [timestamp]

Options:
- Run `/document` for interactive mode to manually add entries
- Run `/document brief` to just update timestamp
- Continue working and document later
```

### Stale Memory Bank Warning

If last update was >24 hours ago:

```
⚠ Memory bank is significantly stale (last updated [X] days ago).

Consider doing a comprehensive update to capture all work since then.
Proceeding with interactive mode for thorough documentation.
```

---

## Examples

### Example 1: Auto Mode

```
User: /document auto

Claude:
1. Checks git diff - finds 5 files changed
2. Analyzes conversation - finds 2 decisions made
3. Compares to session.md - 3 of 5 next steps done
4. Presents suggested updates
5. User confirms
6. Applies updates to progress.md and session.md
7. Shows summary
```

### Example 2: Interactive Mode

```
User: /document

Claude:
1. Shows current state (last update 4h ago)
2. Asks: "What work was completed?"
3. User: "Finished the auth module and fixed the login bug"
4. Asks: "Any decisions made?"
5. User: "Decided to use JWT instead of sessions"
6. Asks: "Next steps?"
7. User: "Need to add refresh tokens"
8. Applies updates
9. Shows summary
```

### Example 3: Brief Mode

```
User: /document brief

Claude:
1. Gets timestamp
2. Adds one-line entry: "Continued work on auth module"
3. Updates timestamps
4. Shows brief confirmation
```

---

## Skill Chaining

After documentation:

| Situation | Suggestion |
|-----------|------------|
| End of session | "Run `/handoff` to prepare git commit" |
| Work continues | "Run `/logwork` to log this work" |
| Need direction | "Run `/plan next` to identify next steps" |
| Major milestone | "Consider updating overview.md work area status" |

---

## Checklist

- [ ] Parsed arguments to determine mode
- [ ] Read current memory bank state
- [ ] Detected changes (git, conversation, logs)
- [ ] Gathered information (auto or interactive)
- [ ] Applied updates to progress.md
- [ ] Applied updates to session.md
- [ ] Updated CLAUDE.md if new patterns
- [ ] Ensured cross-file consistency
- [ ] Added timestamps to all updates
- [ ] Showed summary of changes
- [ ] Suggested appropriate next action

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fpontejos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
