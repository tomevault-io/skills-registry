---
name: hindsight
description: Track session learnings and maintain a persistent LEARNINGS.md at the user level (~/.claude/LEARNINGS.md). Use this skill at the END of every Claude Code session to record what worked, what didn't, and a self-assessed session score. Trigger when the user says "end session", "wrap up", "session review", "record learnings", "what did we learn", "hindsight", "/hindsight", or when a session is clearly ending. Also use when the user asks to view past learnings or trends. Use when this capability is needed.
metadata:
  author: simhskal
---

# Hindsight

Record session outcomes, patterns, and scores to build a persistent knowledge base across all Claude Code sessions.

## File Location

All learnings are stored in `~/.claude/LEARNINGS.md` (user-level, shared across all projects).

## When to Record

Record a session entry when:
- The user explicitly asks to end/wrap up a session
- The user asks for a session review or to record learnings
- The user invokes `/session-learnings`

## Session Entry Format

Append each entry to `~/.claude/LEARNINGS.md`, creating the file if it doesn't exist.

```markdown
---

## Session: YYYY-MM-DD HH:MM — [Brief Title]

**Project:** [repo/project name or "General"]
**Branch:** [git branch if applicable]
**Duration estimate:** [short/medium/long based on conversation length]

### What Worked
- [Specific thing that went well — be concrete]
- [Tool/approach/pattern that was effective]

### What Didn't Work
- [What failed, was slow, or caused friction]
- [Approaches that were abandoned and why]

### Key Learnings
- [Reusable insight for future sessions]
- [Pattern discovered, gotcha avoided, or technique learned]

### Session Score: X/10

| Dimension         | Score | Notes                        |
|-------------------|-------|------------------------------|
| Task completion   | X/10  | [Did we finish what was asked?] |
| Code quality      | X/10  | [Clean, tested, follows patterns?] |
| Efficiency        | X/10  | [Minimal tool calls, no wasted effort?] |
| Communication     | X/10  | [Clear, concise, helpful?] |
| **Overall**       | **X/10** | |

### Follow-ups
- [ ] [Any unfinished work or next steps]
```

## Score Dimensions

- **Task completion (40%)**: Did the session accomplish what was asked? 10 = fully done, 1 = nothing completed.
- **Code quality (25%)**: Is the code clean, tested, following repo conventions? 10 = production-ready, 1 = broken.
- **Efficiency (20%)**: Minimal unnecessary steps? 10 = direct path, 1 = excessive searching/retries.
- **Communication (15%)**: Clear, concise, helpful? 10 = perfect clarity, 1 = confusing/verbose.
- **Overall**: Weighted average, rounded to nearest 0.5.

## Process

1. Read `~/.claude/LEARNINGS.md` if it exists (check format and see past entries)
2. Reflect on the current session: what was accomplished, what tools/approaches worked, what caused friction
3. Generate the entry using the template
4. If the file doesn't exist, create it with this header before the first entry:

```markdown
# Session Learnings

Persistent record of Claude Code session outcomes, patterns, and scores.

| Date | Project | Score | Title |
|------|---------|-------|-------|
```

5. Add a row to the summary table at the top for the new session
6. Append the full entry after the table (newest entries first, right after the table)
7. Show the user a summary of the entry

## Querying Past Learnings

When the user asks "show my learnings", "what patterns have I seen", or "learnings summary":

1. Read `~/.claude/LEARNINGS.md`
2. Summarize: average scores, recurring themes in "What Worked" / "What Didn't", top learnings
3. Highlight score trends (improving/declining over time)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/simhskal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
