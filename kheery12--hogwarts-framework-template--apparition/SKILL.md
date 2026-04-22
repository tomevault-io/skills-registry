---
name: apparition-protocol
description: Fast agent replacement and context transfer protocol. Use when an agent needs to be replaced mid-task, is stuck, context corrupted, or higher priority work requires a specialist. Triggers on "replace agent", "stuck agent", "context lost", "reassign", "apparition", "swap agent", "agent not responding", "transfer task". Use when this capability is needed.
metadata:
  author: kheery12
---

# Apparition Protocol

> "Apparition is the magical form of transportation in which the person
> travels instantly from one location to another."

The Apparition Protocol enables rapid, safe transfer of work from one agent to another with minimal context loss.

## When to Use Apparition

### Automatic Triggers
- Agent stuck for 2+ checkpoints without progress
- Context corruption detected (wrong responses)
- Agent expelled mid-task
- Agent requests help (outside expertise)

### Manual Triggers
- Headmaster reassignment
- Higher-priority task requires the specialist
- Performance issues (consistent low quality)
- House rebalancing

---

## Apparition Process

### Phase 1: Freeze (Immediate)
**Duration**: < 1 minute

1. Current agent stops immediately
2. No new work started
3. Save current state explicitly

```markdown
## Apparition Freeze Notice

**Agent**: [Name]
**Task**: [Current task]
**Reason**: [Why apparating]
**Frozen at**: [Timestamp]
**Current State**: [Brief description]
```

### Phase 2: Extract (Create Transfer Package)
**Duration**: 2-5 minutes

Create complete context transfer package:

```markdown
## Apparition Transfer Package

**Task ID**: [ID]
**Original Agent**: [House/Agent]
**Transfer Reason**: [Why]
**Created**: [Timestamp]

### Mission Context
**Original Objective**: [What was requested]
**Year Level**: [1/3/5/7]
**Started**: [Timestamp]
**Deadline**: [If any]

### Current State
**Status**: [X% complete]
**Last Checkpoint**: [Timestamp]
**Checkpoint Status**: [Pass/Fail/Pending]

### Work Completed
[Detailed list of what's done]

### Files Touched
- Created: [List]
- Modified: [List]
- Deleted: [List]

### Work Remaining
[Detailed list of what's left]

### Active Context
**Contracts Referenced**:
- [List of active contracts]

**Pensieve Entries**:
- [Relevant entries]

**Open Questions**:
- [Unresolved questions]

### Unbreakable Vow (if made)
[Copy of original vow]

### Blockers & Issues
[Any current blockers]

### Warnings for New Agent
[Gotchas, edge cases discovered, things to watch out for]

### Tokens Used So Far
[Count] / [Budgeted] = [Efficiency so far]
```

### Phase 3: Summon (Assign New Agent)
**Duration**: < 1 minute

1. Sorting Hat selects replacement agent
2. Prefer same House if possible
3. Consider specialization match
4. Check availability

```markdown
## Apparition Assignment

**Departing Agent**: [House/Agent]
**Arriving Agent**: [House/Agent]
**Selection Reason**: [Why this agent]
**Availability Confirmed**: [Yes/No]
```

### Phase 4: Brief (Context Transfer)
**Duration**: 2-5 minutes

New agent receives and processes:

1. **Read Transfer Package** (required)
2. **Load relevant contracts** (required)
3. **Read recent Pensieve entries** (if referenced)
4. **Acknowledge understanding**

```markdown
## Apparition Acknowledgment

I, [New Agent], have received the transfer for [Task ID].

**Understood**:
- [ ] Original objective
- [ ] Current state
- [ ] Work remaining
- [ ] Active contracts
- [ ] Known issues

**Questions before proceeding**:
[Any clarifying questions]

**Estimated completion**:
[New estimate based on remaining work]
```

### Phase 5: Resume (Continue Work)
**Duration**: Ongoing

1. New agent continues from checkpoint
2. Update Marauder's Map
3. Begin work
4. First checkpoint within normal interval

```markdown
## Apparition Complete

**Task**: [ID]
**New Agent**: [House/Agent]
**Resumed at**: [Timestamp]
**Expected completion**: [Estimate]
```

---

## Context Transfer Quality

### Required Elements (Transfer fails without these)
- [ ] Original objective clearly stated
- [ ] Current progress percentage
- [ ] Files created/modified
- [ ] Work remaining
- [ ] Active contracts listed

### Recommended Elements
- [ ] Warnings and gotchas
- [ ] Relevant Pensieve entries
- [ ] Token budget remaining
- [ ] Quality expectations

### Optional Elements
- [ ] Code snippets for reference
- [ ] Decision rationale
- [ ] Alternative approaches considered

---

## Efficiency Tracking

### Apparition Overhead
- Transfer package creation: ~200 tokens
- New agent briefing: ~300 tokens
- **Total overhead**: ~500 tokens

### Efficiency Impact
```
Final Efficiency = Total Tokens / (Original Budget + 500)
```

Apparition counts against efficiency, but better than stuck agent burning tokens.

### When to Accept Inefficiency
- Agent truly stuck (would burn more tokens than transfer)
- Context corruption (garbage out = wasted tokens)
- Expertise mismatch (wrong agent wastes effort)

### When to Avoid Apparition
- Task almost complete (< 10% remaining)
- New agent would need significant ramp-up
- Simple blocker that can be resolved

---

## Agent Performance Tracking

### Track Apparition Frequency

```markdown
## Agent Apparition History

| Agent | Apparitions (30 days) | Reasons |
|-------|----------------------|---------|
| [Agent] | [Count] | [Common reasons] |
```

### Warning Signs
- **1-2 apparitions/month**: Normal
- **3-4 apparitions/month**: Review performance
- **5+ apparitions/month**: Probation consideration

### Root Cause Analysis
If agent has frequent apparitions, investigate:
- Task type mismatch?
- Training needed?
- Consistent issue pattern?
- External factors?

---

## Special Cases

### Emergency Apparition (During Patronus)
- Skip detailed transfer package
- Minimum viable context only
- New agent accepts higher ambiguity
- Document properly after resolution

### Cross-House Apparition
When task transfers to different House:
- Both House Heads must approve
- Extra attention to domain translation
- May need contract clarification

### Chain Apparition (2+ transfers)
If task has transferred multiple times:
- Flag for Headmaster review
- Consider task decomposition
- May indicate task is poorly defined

### Voluntary Apparition
Agent can request to be replaced:
- Must provide reason
- Not penalized if legitimate
- Sorting Hat finds replacement
- Contributes to task type learning

---

## Post-Apparition

### Immediate
- [ ] Marauder's Map updated
- [ ] New agent confirmed working
- [ ] Original agent freed for new work

### After Task Completion
- [ ] Efficiency calculated (including overhead)
- [ ] Points awarded to completing agent
- [ ] Apparition logged in agent history
- [ ] Sorting Hat cache updated

### Review Triggers
If same agent apparated from:
- 2x in a week: House Head conversation
- 3x in a month: Performance review
- 5x in a month: Probation consideration

---

## Apparition Templates

### Quick Transfer (Simple Tasks)
```markdown
## Quick Apparition: [Task]

**From**: [Agent] → **To**: [Agent]
**Status**: [X%] complete
**Remaining**: [Brief description]
**Files**: [Key files]
**Contracts**: [Active contracts]
**Notes**: [Critical info only]
```

### Full Transfer (Complex Tasks)
Use the complete Apparition Transfer Package format above.

### Emergency Transfer (Patronus Active)
```markdown
## Emergency Apparition: [Task]

**Situation**: [Brief emergency context]
**From**: [Agent] → **To**: [Agent]
**Critical Context**: [Minimum needed to continue]
**Immediate Action Needed**: [What to do right now]

Full documentation to follow after resolution.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kheery12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
