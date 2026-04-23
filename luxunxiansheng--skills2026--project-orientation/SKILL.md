---
name: project-orientation
description: Use when starting or resuming work to quickly understand project state, active decisions, and immediate next steps.
metadata:
  author: luxunxiansheng
---

# Project Orientation

## Overview

This skill helps agents quickly understand where a project is, where it's going, and what needs to happen next - without reviewing the entire codebase or documentation from scratch.

## When to Use

**Automatically:**
- After git commits (can be integrated into git hooks)
- When major milestones are completed

**Manually:**
- When starting a new session
- When switching between different AI agents
- When returning to a project after time away
- When onboarding new team members

## The Process

### Reading Project State (Orient Mode)

When invoked without arguments, read and synthesize:

1. **Check for state file**: Look for `project/STATE.md`
2. **Analyze git context**:
   - Current branch and recent commits (last 10)
   - Active branches and their purposes
   - Recent merge activity
3. **Scan key documentation**:
   - README.md for project overview
   - ROADMAP.md if it exists
   - docs/ directory for recent changes
4. **Present concise summary**:
   - Current focus area (what's being worked on now)
   - Recent completions (what just finished)
   - Next steps (what's coming up)
   - Active decisions or blockers
   - Key context for continuity

### Updating Project State (Update Mode)

When invoked with `--update` or after a commit:

1. **Gather context**:
   - What was just completed (from commit or user input)
   - What changed (technical details)
   - Why it changed (decisions, rationale)
   - What's next (immediate next steps)
   - Any blockers or open questions

2. **Update `project/STATE.md`**:
   - Append to history
   - Update current focus
   - Refresh next steps
   - Document decisions made
   - Note any blockers

3. **Keep it concise**:
   - Focus on "why" not just "what"
   - Include enough context for continuity
   - Remove stale information
   - Keep file under 500 lines

## File Format: `project/STATE.md`

The state file is structured for both human and LLM readability:

```markdown
# Project State

Last Updated: YYYY-MM-DD HH:MM
Current Branch: feature/xyz

## Current Focus

[What's being worked on right now - 2-3 sentences]

## Recent Completions

- YYYY-MM-DD: [What was completed and why it matters]
- YYYY-MM-DD: [What was completed and why it matters]

## Next Steps

1. [Immediate next task with context]
2. [Following task with context]
3. [Upcoming task with context]

## Active Decisions & Context

### [Decision/Topic Name]
- **Context**: Why this matters
- **Options Considered**: Brief summary
- **Current Direction**: What was chosen and why
- **Open Questions**: What's still unclear

## Blockers

- [Blocker description and impact]

## Key Technical Context

### Architecture Notes
- [Important architectural decisions]
- [Patterns being followed]

### Dependencies & Integration
- [Key dependencies and their status]
- [Integration points to be aware of]

## History (Last 30 Days)

### YYYY-MM-DD: [Milestone/Feature Name]
- **What**: Brief description
- **Why**: Rationale
- **Impact**: What changed
- **Decisions**: Key choices made
```

## Usage Examples

### Starting a Session
```
User: /project-orientation
Agent: [Reads project/STATE.md and presents summary]
```

### After a Commit (Manual)
```
User: /project-orientation --update
Agent: [Asks about what was completed, updates state file]
```

### Git Hook Integration
Add to `.git/hooks/post-commit`:
```bash
#!/bin/bash
# Optionally trigger project state update
# <your-agent-cli> skill project-orientation --update --auto
```

## Key Principles

- **Concise but Complete**: Include enough context for continuity, not exhaustive details
- **Why Over What**: Focus on rationale and decisions, not just changes
- **Living Document**: Keep it current, remove stale info
- **LLM-Friendly**: Structure for easy parsing and synthesis
- **Human-Readable**: Should be useful for humans too
- **No External Dependencies**: Pure file-based, works offline

## Implementation Notes

When implementing this skill:

1. **Create state file if missing**: Initialize with current project analysis
2. **Preserve history**: Don't delete old entries, just move to history section
3. **Smart summarization**: Condense when file gets too long
4. **Git-aware**: Use git context to infer state when possible
5. **Flexible format**: Allow variations in structure as long as key sections exist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luxunxiansheng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
