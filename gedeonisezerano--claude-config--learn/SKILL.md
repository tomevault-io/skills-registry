---
name: learn
description: Analyze the current session to extract learnings from mistakes and successes. Creates or updates project skills and CLAUDE.md with actionable patterns. Use after completing a task or when you notice repeated friction. Use when this capability is needed.
metadata:
  author: gedeonisezerano
---

# Session Learning Extraction

Analyze the current conversation to identify what went well and what could be improved, then propose actionable learnings to prevent future mistakes.

## Step 1: Run Automated Preprocessing

Run the preprocessing script to automatically locate, extract, and clean the current session:

```bash
~/.claude/skills/learn/run_learn.sh
```

This script will:
1. Find the current project's session directory
2. Identify the most recent session file
3. Run the Python preprocessor to extract meaningful content
4. Output the cleaned conversation to stdout

The preprocessor extracts:
- User messages (the actual prompts)
- Assistant text responses (not tool calls)
- Error messages and failures
- User corrections and feedback

It removes:
- Large tool results (file contents, command outputs)
- Binary/encoded data
- Redundant metadata
- System messages

## Step 2: Analyze Cleaned Session

With the cleaned session text, look for:

### Friction Signals (What Went Wrong)
- **User corrections**: "no", "that's wrong", "instead", "actually", "not what I meant"
- **Repeated attempts**: Same task attempted multiple times
- **Reverts**: User asking to undo or revert changes
- **Explicit feedback**: "don't do X", "always do Y", "remember that..."
- **Failed commands**: Build failures, test failures, errors after Claude's changes
- **Clarifications needed**: User had to explain the same thing multiple times

### Success Signals (What Went Well)
- **First-try success**: Task completed without corrections
- **User approval**: "perfect", "that's right", "exactly"
- **Patterns that worked**: Approaches the user accepted

## Step 4: Generate Learnings

For each identified pattern, structure it as:

```markdown
## Pattern: [Descriptive Name]

**Trigger**: When [specific situation in this codebase]

**Do**: [Correct approach with specifics]

**Avoid**: [What not to do and why]

**Example**: [Reference to actual file/code pattern if applicable]
```

### Learning Quality Criteria
- **Actionable**: Must be specific enough to apply
- **Generalizable**: Should apply to similar future situations, not just one-off cases
- **Non-obvious**: Don't capture things that are standard practice
- **Project-specific**: Focus on patterns unique to this codebase

## Step 5: Present Learnings for Review

Use AskUserQuestion to present each proposed learning and ask:

1. Is this learning accurate and worth remembering?
2. Where should it be stored?
   - **CLAUDE.md** (project root) - For critical rules, always loaded
   - **Project skill** (`.claude/skills/`) - For detailed patterns, loaded on demand
   - **User CLAUDE.md** (`~/.claude/CLAUDE.md`) - For personal preferences across all projects
   - **Discard** - Not worth persisting

## Step 6: Persist Approved Learnings

### For CLAUDE.md additions:
Check if a "Learnings" or "Patterns" section exists. If not, create one. Append the new learning.

### For Project Skills:
Check if `.claude/skills/project-patterns/skill.md` exists:
- If yes: Append the new pattern to the existing file
- If no: Create the skill with proper frontmatter:

```markdown
---
name: project-patterns
description: Learned patterns and conventions specific to this project. Loaded automatically for relevant tasks.
---

# Project-Specific Patterns

Patterns learned from past sessions to maintain consistency and avoid repeated mistakes.

[learnings go here]
```

## Step 7: Summary

After persisting, summarize:
- Number of learnings extracted
- Where each was stored
- Suggest running `/learn` again after future sessions with friction

---

## Important Notes

- Never persist learnings without user approval
- Prefer specific, actionable instructions over vague guidelines
- If a learning contradicts an existing rule, flag it for user decision
- Keep learnings concise - one pattern per learning
- Include file paths or code references when relevant to this specific codebase

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gedeonisezerano) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
