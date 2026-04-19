---
name: start-sprint-wave
description: Launch parallel implementer agents for all RFCs in a sprint wave. Use when starting wave execution. Use when this capability is needed.
metadata:
  author: mathiaschristiansen
---

# Start Sprint $0 Wave $1

Launch parallel implementer agents for all RFCs in Wave $1 of Sprint $0.

## Sprint Context

### Sprint Main File
!`cat .arch/sprints/sprint-$0.md 2>/dev/null | head -50 || echo "Sprint $0 not found - run /init-sprint $0 first"`!

### Wave Status
!`cat .arch/sprints/sprint-$0/WAVE-STATUS.md 2>/dev/null | head -60 || echo "No wave status found"`!

### Context Summary
!`cat .arch/sprints/sprint-$0/CONTEXT.md 2>/dev/null | head -100 || echo "No context file found"`!

### Dependency Graph
!`cat .arch/sprints/sprint-$0/DEPENDENCY-GRAPH.md 2>/dev/null | head -80 || echo "No dependency graph found"`!

## Your Task

### Step 1: Validate Prerequisites

1. **Verify sprint exists**:
   - `.arch/sprints/sprint-$0.md` exists
   - `.arch/sprints/sprint-$0/CONTEXT.md` exists

2. **Verify wave exists**:
   - Read sprint file and confirm Wave $1 is defined
   - Get list of all RFCs in Wave $1

3. **Validate previous waves** (if Wave $1 > 1):
   - Read WAVE-STATUS.md
   - Verify all RFCs in previous waves are "completed"
   - **ERROR if any are incomplete**

4. **Check for blockers**:
   - Read WAVE-STATUS.md for active blockers
   - **WARN if blockers exist**

### Step 2: Prepare Agent Prompts

For each RFC in Wave $1, prepare a prompt using this template:

```markdown
You are implementer agent @implementer-XXX working on Sprint $0, Wave $1.

## Your Assignment

**RFC**: RFC-YYY: [RFC Title]
**RFC File**: .arch/rfcs/approved/YYY-rfc-name.md
**Sprint**: $0
**Wave**: $1
**Estimated Effort**: [X hours]

## Critical Context - Read These First

Before starting, you MUST read these files:

1. **Sprint Context**: .arch/sprints/sprint-$0/CONTEXT.md
   - Database schema, architecture, API patterns, code examples
   - This is your PRIMARY source of context

2. **Your RFC**: .arch/rfcs/approved/YYY-rfc-name.md
   - Complete specification and requirements

3. **Dependency Graph**: .arch/sprints/sprint-$0/DEPENDENCY-GRAPH.md
   - How your RFC relates to others

## Your Workflow

1. **Read context files** - DO NOT SKIP
2. **Claim RFC**: /claim-rfc YYY
3. **Update WAVE-STATUS.md**: Mark as "in-progress"
4. **Implement the RFC**:
   - Follow patterns from CONTEXT.md
   - Write tests as specified
   - Commit changes with descriptive messages
5. **Update progress** every 30-60 minutes
6. **Complete RFC**: /complete-rfc YYY
7. **Update WAVE-STATUS.md**: Mark as "completed"

## Important Notes

- Work independently - avoid file conflicts
- Use context files for patterns
- Test code before marking complete
- Document blockers immediately

## Other Agents in Wave $1

[List other RFCs in this wave]

## Quick Context Reference

[Summary from CONTEXT.md - tech stack, architecture, patterns]
```

### Step 3: Launch Parallel Agents

**CRITICAL**: Launch ALL agents in **a single message** with multiple Task tool calls.

For each RFC in Wave $1:
```
Task tool:
- subagent_type: "general-purpose"
- description: "Implement RFC-XXX: [Title]"
- prompt: [prompt from Step 2]
```

### Step 4: Update Wave Status

1. Update WAVE-STATUS.md:
   - Set Wave $1 status to "in-progress"
   - Set started time
   - Calculate expected completion
   - List all RFCs as "in-progress" with agents

### Step 5: Report

```markdown
## Wave $1 Launched

**RFCs Started**: [List]
**Agents Launched**: [List]
**Expected Completion**: [Timestamp]

**Monitoring**:
- Check .arch/sprints/sprint-$0/WAVE-STATUS.md for progress
- Use /sprint-status for overview

**Next Steps**:
1. Monitor agent progress
2. Wait for Wave $1 completion
3. Start next wave: /start-sprint-wave $0 [next-wave]
```

## Error Handling

**Previous wave not complete**:
```
ERROR: Cannot start Wave $1 - Wave [N-1] is not complete.

Incomplete RFCs:
- RFC-XXX: [Title] - Status: in-progress
```

**Wave doesn't exist**:
```
ERROR: Wave $1 does not exist in Sprint $0.
Available waves: 1, 2, 3
```

**Sprint not initialized**:
```
ERROR: Sprint $0 not found.
Run /init-sprint $0 first.
```

## Important Guidelines

### Context is Critical

Each agent prompt MUST include:
1. Link to CONTEXT.md (most important)
2. Link to RFC file
3. Link to DEPENDENCY-GRAPH.md
4. Quick reference summary

### Launch in Single Message

**DO THIS** ✅:
```
One message with:
- Task: RFC-001
- Task: RFC-002
- Task: RFC-003
```

**DON'T DO THIS** ❌:
```
Message 1: Task RFC-001
Message 2: Task RFC-002
Message 3: Task RFC-003
```

Multiple Task calls in one message = true parallelization.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mathiaschristiansen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
