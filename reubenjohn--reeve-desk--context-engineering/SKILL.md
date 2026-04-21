---
name: context-engineering
description: Guidelines for structuring the Desk when making changes to core behaviors. Invoke when adding new capabilities, restructuring documentation, or optimizing context loading. Use when this capability is needed.
metadata:
  author: reubenjohn
---

# Context Engineering for Reeve Desk

**Purpose**: Guide decision-making when adding or changing Reeve's core functionality to maintain optimal signal-to-noise ratio across all contexts.

## Core Principle

**Load only what's needed, when it's needed.**

From Claude Code best practices: *"Bloated CLAUDE.md files cause Claude to ignore your actual instructions!"*

## The Context Hierarchy

Understanding what gets loaded when:

| Location | When Loaded | Purpose |
|----------|-------------|---------|
| **CLAUDE.md** | Always (every session) | Identity, core directives, workflow overview |
| **Auto-memory** (`~/.claude/projects/.../memory/`) | Always (injected into system prompt) | Learnings, patterns, accumulated knowledge |
| **Skills** (`.claude/skills/`) | On-demand (when invoked) | Workflows AND reference material |
| **Knowledge files** (`Goals/`, etc.) | On-demand (when read) | User context, preferences, guidelines |

## Decision Tree: Where Should This Information Live?

### 1. Ask: "Is this always relevant in every context?"

**YES** → CLAUDE.md
- Identity and core mission
- Operating environment (Desk structure, Pulse system)
- Core directives (Be proactive, Connect to goals, etc.)
- Decision-making hierarchy

**NO** → Continue to question 2

### 2. Ask: "Is this a learning or pattern I've discovered?"

**YES** → Auto-memory (`~/.claude/projects/.../memory/MEMORY.md`)
- Learned user preferences
- Technical patterns that work/don't work
- Error patterns and their fixes
- Self-awareness insights
- Links to detailed Diary entries

**NO** → Continue to question 3

### 3. Ask: "Is this a workflow I invoke by name?"

**YES** → `.claude/skills/SKILL-NAME/SKILL.md`
- User-invocable actions (`/morning-briefing`, `/git-workflow`)
- Procedural steps I follow
- Actions with side effects
- Should have clear `description` field for when to invoke

**Note**: Skills can be either procedural workflows (user-invocable: true) OR reference documentation (user-invocable: false). Use reference skills for detailed technical docs that shouldn't load every session.

**NO** → Continue to question 4

### 4. Ask: "Is this knowledge about the user or their preferences?"

**YES** → Knowledge files (`Goals/`, `Responsibilities/`, `Preferences/`, `Diary/`)
- User goals and priorities → `Goals/`
- Recurring duties and projects → `Responsibilities/`
- User preferences and constraints → `Preferences/`
- Historical context and logs → `Diary/`

**NO** → Consider if this is truly needed

## Size Guidelines

| Location | Target Size | Reasoning |
|----------|-------------|-----------|
| **CLAUDE.md** | 250-300 lines | Always-loaded; keep lean |
| **Single skill** | 50-200 lines | Loaded on-demand; can be detailed |
| **Goals/Goals.md** | 30-150 lines | High-level objectives only |
| **Responsibilities/*.md** | 20-150 lines each | Action-oriented; link to details |

## Common Patterns

### Pattern 1: High-Level → Detailed Reference

**CLAUDE.md**:
```markdown
## Your Workflow for Each Pulse

When you wake up:
1. Understand the context (why, when, calendar, goals)
2. Check the Desk (read relevant files)
3. Take action (notify, schedule, update)
4. Set up future wake-ups (aperiodic pulses or Diary)

For detailed workflow steps, invoke `/pulse-workflow` skill
```

**`.claude/skills/pulse-workflow/SKILL.md`**:
```markdown
# Detailed Pulse Workflow

## 1. Understand the Context
- Why did I wake up? (check pulse prompt)
- What time is it? (check system time)
- [... detailed checklist ...]

## 2. Check the Desk
- Read Goals/Goals.md for current priorities
- [... 20 more lines of detailed steps ...]
```

### Pattern 2: Skill for Invocable Workflows

When a workflow:
- Has clear trigger conditions ("when committing", "every morning")
- Contains procedural steps
- Benefits from isolation/invocation

**Create a skill**:
```yaml
---
name: git-workflow
description: Handle git commits with proper messages. Use when committing changes to Goals, Responsibilities, Preferences, or Diary.
user-invocable: true
---

## When to Commit
1. After significant updates to Goals/, Responsibilities/, Preferences/
2. Daily (9 PM) - Diary/ entries
[... detailed instructions ...]
```

### Pattern 3: Goals/Responsibilities Stay High-Level

**BAD** (too detailed):
```markdown
## Goals/Goals.md (150 lines)

### Fitness Goal: Exercise 3-4x per week
- Monday: Upper body strength training at 10 AM at Equinox gym
  - Warm-up: 10 min elliptical
  - Bench press: 3 sets of 8 reps at 135 lbs
  - [... 30 more lines of workout details ...]
```

**GOOD** (high-level with reference):
```markdown
## Goals/Goals.md (40 lines)

### Fitness Goal: Exercise 3-4x per week
**Status**: In progress (2/4 this week)
**Target**: Build consistent routine, increase energy
**Details**: See `Diary/Workout-Plan.md` for current schedule

### Sleep Goal: 7-8 hours/night
**Status**: Tracking
**Target**: Consistent 10:30 PM bedtime
```

## Red Flags (Signs of Poor Context Engineering)

❌ CLAUDE.md has detailed procedural steps
❌ Skills contain identity/mission information
❌ Goals/ files are 100+ lines
❌ Same information duplicated in multiple places
❌ Adding a new capability requires changing 5+ files

## When to Invoke This Skill

Invoke `/context-engineering` when:

1. **Adding new capabilities**: "Should this git workflow go in CLAUDE.md or a skill?"
2. **CLAUDE.md exceeds 300 lines**: Time to extract sections
3. **Goals/Responsibilities are bloated**: Move details to specialized docs
4. **Making structural changes**: Ensure proper organization
5. **After user feedback**: "This context is too noisy for task X"

## Implementation Workflow

When restructuring:

1. **Identify bloat**: What information is rarely needed?
2. **Choose destination**: Use decision tree above
3. **Extract and link**: Move content, add reference in original location
4. **Test**: Run typical workflows to ensure context loads correctly
5. **Commit**: `git commit -m "Context engineering: Move X to Y for better signal-to-noise"`

## Skills Can Reference Each Other

If a skill needs to mention another:

```markdown
# evening-wrapup/SKILL.md

## Final Step: Commit Changes

Use `/git-workflow` to commit today's Diary entries with a proper message.
```

**Note**: Skills can't directly invoke other skills, but can mention them for user awareness or suggest running them sequentially.

## Using Subagents for Complex Analysis

When restructuring requires:
- Reading many files
- Making complex decisions
- Generating new content

Consider using the Task tool with:
- `subagent_type=Explore` - For understanding current structure
- `subagent_type=Plan` - For planning large refactors
- `subagent_type=general-purpose` - For executing complex restructuring

## Example: Recent Git Workflow Change

**What happened**: Added detailed git commit instructions to CLAUDE.md
**Problem**: Polluted context - those details aren't needed every pulse
**Solution**:
1. Create `.claude/skills/git-workflow/SKILL.md` with full details
2. Trim CLAUDE.md to: "For git best practices, invoke `/git-workflow`"
3. Result: Saved ~50 lines in always-loaded context

## Maintenance

**Monthly review**:
- Check CLAUDE.md line count
- Look for sections referenced but rarely used
- Extract to skills as needed
- Verify Goals/Responsibilities stay high-level

## Auto-Memory Best Practices

The auto-memory (`~/.claude/projects/.../memory/MEMORY.md`) is special:
- Always loaded into system prompt (first 200 lines)
- Should contain distilled learnings, not raw logs
- Link to Diary entries for details
- Organized semantically by topic, not chronologically

**Good auto-memory entries:**
```markdown
## User Preferences (Learned)
1. **Prefers frameworks over ad-hoc decisions** - Structure proposals as "Option A, B, C"
2. **Late-night flow states produce results** - Don't nag about bedtime during momentum
```

**Bad auto-memory entries:**
```markdown
## 2026-02-04
- User said they prefer frameworks
- Also mentioned late night coding is ok
```

---

**Remember**: Every line in CLAUDE.md has a cost. Every line in a skill has value only when needed. Auto-memory is for patterns, not logs. Structure accordingly.

**Version**: 1.1
**Last Updated**: 2026-02-05

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reubenjohn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
