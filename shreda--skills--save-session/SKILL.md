---
name: save-session
description: Create a session summary file documenting what was worked on and next steps. Saves to .claude-sessions/ at project level (if in a project) or ~/.claude-sessions/ (user level). Use when this capability is needed.
metadata:
  author: shreda
---

# Save Session

Create a timestamped session summary documenting the work completed and next steps.

**Brief Description**: $ARGUMENTS

---

## Step 1: Determine Location

Check if we're in a project directory (has a git repo, package.json, or similar project markers):

1. **Project level**: If in a project directory, use `./.claude-sessions/`
2. **User level**: Otherwise, use `~/.claude-sessions/` (expand to appropriate home directory for the OS)

Create the sessions directory if it doesn't exist.

---

## Step 2: Generate Filename

Create the filename using this format:
```
yyyymmddhhmm-session-[brief-description].md
```

- Use current timestamp in `yyyymmddhhmm` format (24-hour time)
- Convert the brief description to kebab-case (lowercase, spaces to hyphens)
- Remove special characters
- Truncate to keep filename reasonable (max ~50 chars for description part)

**Example**: `202602051430-session-csrf-script-review.md`

If no description provided via $ARGUMENTS, analyze the conversation to generate one automatically (2-4 words).

---

## Step 3: Analyze the Session

Review the conversation history to identify:

1. **What was worked on**:
   - Files created or modified
   - Features implemented
   - Bugs fixed
   - Research conducted
   - Decisions made

2. **Key outcomes**:
   - What was accomplished
   - Any blockers encountered
   - Important discoveries or learnings

3. **Next steps**:
   - Unfinished tasks
   - Follow-up items mentioned
   - Natural continuation points

---

## Step 4: Write Summary

Create the summary file with this structure:

```markdown
# Session Summary

**Date**: [Full date and time]
**Project**: [Project name or "User-level session"]
**Description**: [Brief description]

---

## What We Worked On

- [Bullet point list of main activities]
- [Be specific about files, features, decisions]

## Key Outcomes

- [What was accomplished]
- [Important decisions made]
- [Any issues resolved]

## Files Changed

- `path/to/file.ext` - [brief description of change]
- [List any files created, modified, or deleted]

## Next Steps

- [ ] [Actionable next step 1]
- [ ] [Actionable next step 2]
- [Continue as needed]

## Notes

[Any additional context, learnings, or things to remember for next session]

---

*Session ID: [filename without .md]*
```

---

## Step 5: Confirm

After saving, report to the user:
- Full path where the summary was saved
- Quick overview of what was captured
- Remind them they can resume with `/resume-session`

---

## Important Rules

1. **Be thorough but concise**: Capture enough detail to resume effectively
2. **Focus on actionable items**: Next steps should be clear and actionable
3. **Include file paths**: Make it easy to find what was changed
4. **Generate description if missing**: Don't require the user to provide one
5. **Create directory if needed**: Don't fail because the sessions folder doesn't exist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shreda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
