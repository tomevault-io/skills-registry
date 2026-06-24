---
name: strategic-compact
description: Context management intelligence for Claude Code sessions. ACTIVATE when context filling up, phase transitions detected, user mentions "running out of context" or "conversation too long". Suggests strategic compaction timing - manual compact at phase transitions beats auto-compact at arbitrary points. Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Strategic Compact

Intelligent context management that suggests compaction at optimal moments.

## Core Principle

**Manual compaction at strategic points > Auto-compaction at arbitrary points**

Auto-compact triggers at context limits, often mid-task. Strategic compaction preserves context through logical phases and compacts at natural transition points.

## When to Suggest Compaction

### Optimal Compaction Points

| Trigger | Reason |
|---------|--------|
| Exploration complete, implementation starting | Exploration context rarely needed for coding |
| Milestone completed | Clean slate for next phase |
| Plan finalized and documented | Plan captured, context can reset |
| Debug session resolved | Debug traces clutter future work |
| Switching to unrelated task | Previous context not relevant |
| 50+ tool calls in session | Accumulated context likely stale |

### Avoid Compaction During

| Scenario | Why |
|----------|-----|
| Mid-implementation | Loses code context and decisions |
| Active debugging | Loses diagnostic information |
| Incomplete task | May need earlier context |
| Code review in progress | Loses review thread |

## Compaction Options

### /compact (Quick)
- Built-in summarization
- Frees context immediately
- May lose some nuance

### /handoff (Recommended for complex work)
Creates HANDOFF.md with:
- Current goal and progress
- What worked / what didn't
- Next steps
- Key decisions made

Then start fresh with: `> HANDOFF.md`

### /clear (Fresh start)
- Complete reset
- Best when switching tasks entirely

## Phase Detection Patterns

Detect phase transitions by monitoring:

```
EXPLORATION → IMPLEMENTATION
- Many Read/Grep/Glob calls → Edit/Write calls starting
- Questions answered → Code being written

IMPLEMENTATION → TESTING
- Edit/Write heavy → Bash(test) calls
- Feature code done → Verification starting

DEBUGGING → RESOLUTION
- Error investigation → Fix confirmed working
- Multiple attempts → Success achieved
```

## Automatic Behavior

This skill works with hooks that:

1. **Track tool usage** - Counts tool calls per session
2. **Detect phase transitions** - Monitors tool patterns
3. **Inject suggestions** - Adds context-aware recommendations

## Usage Patterns

### After Exploration
```
[Hook detects: 30+ Read/Grep calls, first Edit coming]
→ Suggest: "Exploration complete. Consider /compact before implementation."
```

### After Milestone
```
[Hook detects: Tests passing after implementation]
→ Suggest: "Milestone reached. Good time for /compact or /handoff."
```

### Context Threshold
```
[Hook detects: 50 tool calls reached]
→ Suggest: "Session has 50+ tool calls. Consider /compact if context feels stale."
```

## Integration

Works automatically via plugin hooks. No manual configuration needed.

The hooks:
- Run on PreToolUse to track and suggest
- Run on PreCompact to add custom instructions
- Provide non-blocking suggestions via stderr

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
