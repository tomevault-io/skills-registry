---
name: ralph-wiggum
description: Implements Geoffrey Huntley's Ralph Wiggum autonomous iteration technique for managing LLM context. Use when working on long-running tasks, when context is getting polluted, or when you need autonomous development with deliberate context rotation. Treats LLM context like memory - rotates to fresh context before pollution builds up, with state persisting in files and git. Use when this capability is needed.
metadata:
  author: vast-studios
---

# Ralph Wiggum Method

An autonomous AI development technique that treats LLM context like memory, rotating to fresh context before pollution builds up.

## Core Principle

**The malloc/free Problem:**
- Reading files, tool outputs, conversation = `malloc()` (allocates context)
- There is no `free()` - context cannot be selectively released
- Only way to free: start a new conversation

**Ralph's Solution:** Deliberately rotate to fresh context before pollution builds up. State lives in files and git, not in the LLM's memory.

## When to Use

Use Ralph Wiggum when:
- Working on complex, multi-step tasks
- Context window is filling up (>60k tokens)
- Agent is repeating failed attempts
- You need autonomous iteration on a task
- Context pollution is causing confusion

## Setup

### 1. Create State Files

```bash
mkdir -p .ralph
```

Create these files:

**`.ralph/progress.md`** - What's been accomplished:
```markdown
# Progress

## Completed
- [x] Initial setup
- [x] Basic structure created

## In Progress
- [ ] Feature implementation

## Next Steps
- Complete feature tests
```

**`.ralph/guardrails.md`** - Lessons learned (Signs):
```markdown
# Guardrails (Signs)

## Sign: Check imports before adding
- **Trigger**: Adding a new import statement
- **Instruction**: First check if import already exists in file
- **Added after**: Duplicate import caused build failure
```

**`RALPH_TASK.md`** - Task definition with checkboxes:
```markdown
---
task: Build feature X
test_command: "npm test"
---

# Task: Feature X

## Success Criteria

1. [ ] Feature works correctly
2. [ ] Tests pass
3. [ ] Documentation updated

## Context
- Use framework Y
- Follow pattern Z
```

### 2. Initialize Git (if not already)

```bash
git init
git add .ralph/ RALPH_TASK.md
git commit -m "ralph: initialize task"
```

## The Loop

### Iteration Process

1. **Read State** (not from previous context):
   - Read `RALPH_TASK.md` for task definition
   - Read `.ralph/progress.md` for what's done
   - Read `.ralph/guardrails.md` for lessons learned
   - Check git history for recent changes

2. **Work on Unchecked Criteria**:
   - Focus on `[ ]` items in `RALPH_TASK.md`
   - Follow guardrails from `.ralph/guardrails.md`
   - Make incremental progress

3. **Commit Progress**:
   ```bash
   git add -A
   git commit -m "ralph: [criterion] - description"
   ```

4. **Update State Files**:
   - Update `.ralph/progress.md` with accomplishments
   - If errors occur, add to `.ralph/guardrails.md`

5. **Monitor Context**:
   - Track token usage (approximate)
   - At ~70k tokens: warn to wrap up current work
   - At ~80k tokens: **ROTATE** to fresh context

### Context Rotation

When approaching token limits:

1. **Commit all work**:
   ```bash
   git add -A
   git commit -m "ralph: checkpoint before rotation"
   git push  # if remote exists
   ```

2. **Signal rotation**: Output `<ralph>ROTATE</ralph>`

3. **Next iteration**: Start fresh, read state from files/git

## Guardrails (Signs)

When something fails, add a "Sign" to `.ralph/guardrails.md`:

```markdown
### Sign: [Brief description]
- **Trigger**: When this situation occurs
- **Instruction**: What to do differently
- **Added after**: Iteration X - what went wrong
```

Future iterations read guardrails first and follow them.

## Completion Detection

Task is complete when:
1. All `[ ]` in `RALPH_TASK.md` are `[x]`
2. Agent outputs `<ralph>COMPLETE</ralph>`
3. All tests pass (if `test_command` specified)

## Gutter Detection

Detect when stuck:
- Same command failed 3+ times → GUTTER
- Same file written 5+ times in short period → GUTTER
- Agent outputs `<ralph>GUTTER</ralph>`

When gutter detected:
1. Check `.ralph/guardrails.md` for patterns
2. Fix issue manually or add guardrail
3. Re-run iteration

## Token Tracking

Approximate tracking:
- File read: ~1KB per 100 lines
- File write: ~1KB per 100 lines
- Tool calls: ~500 bytes each
- Conversation: ~100 bytes per message

Monitor and rotate before 80k tokens.

## Workflow Example

```bash
# Iteration 1
# Read RALPH_TASK.md, progress.md, guardrails.md
# Work on first [ ] item
# Commit: git commit -m "ralph: implement feature X"
# Update progress.md
# Token count: ~45k → continue

# Iteration 2 (after rotation)
# Read RALPH_TASK.md, progress.md, guardrails.md (fresh context)
# Read git history to see previous work
# Work on next [ ] item
# Commit: git commit -m "ralph: add tests"
# Update progress.md
# Token count: ~78k → ROTATE signal

# Iteration 3 (fresh context)
# Read state files again
# Continue from git history
# Complete remaining items
# All [x] → COMPLETE
```

## Best Practices

1. **Commit frequently**: After each meaningful change
2. **Update progress.md**: After completing each criterion
3. **Add guardrails**: When errors occur, document the lesson
4. **Be specific**: Each criterion should be testable and achievable
5. **Rotate proactively**: Don't wait until context is completely full
6. **Use git history**: Next iteration learns from commits, not context

## Key Files Reference

| File | Purpose | Who Uses It |
|------|---------|-------------|
| `RALPH_TASK.md` | Task definition + success criteria | You define, agent reads |
| `.ralph/progress.md` | What's been accomplished | Agent writes after work |
| `.ralph/guardrails.md` | Lessons learned (Signs) | Agent reads first, writes after failures |
| `.ralph/activity.log` | Tool call log (optional) | For monitoring |
| `.ralph/errors.log` | Failure log (optional) | For debugging |

## Signals

Use these XML-like signals in your output:

- `<ralph>ROTATE</ralph>` - Request context rotation
- `<ralph>COMPLETE</ralph>` - Task is complete
- `<ralph>GUTTER</ralph>` - Agent is stuck, needs intervention
- `<ralph>WARN</ralph>` - Approaching token limit, wrap up current work

## References

- [Original Ralph technique](https://ghuntley.com/ralph/) - Geoffrey Huntley
- [Context as memory](https://ghuntley.com/allocations/) - The malloc/free metaphor

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vast-studios) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
