---
name: sorting-hat-protocol
description: Agent assignment and task routing protocol. Use when starting a new mission, assigning tasks to Houses, or determining which agent should handle work. Triggers on "assign", "who should", "which house", "sort", "delegate", "route task", "new mission", "form team". Use when this capability is needed.
metadata:
  author: kheery12
---

# Sorting Hat Protocol

> "Oh, you may not think I'm pretty, but don't judge on what you see.
> I'll eat myself if you can find a smarter hat than me."

The Sorting Hat determines which House and Agent is best suited for each task.

## Sorting Process

### Step 1: Analyze the Task
Identify the primary domain:
- **UI/UX/Frontend** → Gryffindor
- **API/Database/Architecture** → Ravenclaw
- **Testing/Security/QA** → Slytherin
- **DevOps/Docs/Infrastructure** → Hufflepuff

### Step 2: Check Specialization Matrix
Consult `logs/house-cup/specialization-matrix.md` for performance history.

```markdown
## House Specialization Matrix

| Domain | Gryffindor | Ravenclaw | Slytherin | Hufflepuff |
|--------|------------|-----------|-----------|------------|
| UI Components | 94% | 72% | 68% | 65% |
| API Design | 71% | 96% | 75% | 70% |
| Database | 65% | 93% | 78% | 72% |
| Security | 70% | 82% | 97% | 75% |
| Testing | 72% | 80% | 95% | 78% |
| DevOps | 68% | 75% | 72% | 96% |
```

Assign to House with highest score in relevant domain.

### Step 3: Check Sorting Hat Cache
For specific task types, consult `logs/house-cup/sorting-hat-cache.md`.

```markdown
### Task Type: [Type]
Best Performers:
1. [House]/[Agent] (avg efficiency, quality)
2. [House]/[Agent] (avg efficiency, quality)
```

Assign to highest-performing agent for this task type.

### Step 4: Check Agent Availability
Consult `logs/marauders-map.md` to see who is available.

---

## Sorting Decision Tree

```
START
  │
  ├─ Is this a single-domain task?
  │   ├─ YES → Assign to best House for that domain
  │   └─ NO → Continue
  │
  ├─ Is this a cross-domain task?
  │   ├─ YES → Form multi-House Order
  │   │         └─ Primary House = highest domain score
  │   │         └─ Supporting Houses = other relevant domains
  │   └─ NO → Continue
  │
  ├─ Is this an emergency (Red/Orange threat)?
  │   ├─ YES → Form Dumbledore's Army (best from each House)
  │   └─ NO → Continue
  │
  └─ Default: Assign to House with best overall efficiency
```

---

## Multi-House Orders

When tasks require multiple Houses:

### Primary House
- Leads the mission
- Makes tie-breaking decisions
- Reports to Headmaster

### Supporting Houses
- Execute their domain tasks
- Report to Primary House Prefect
- Follow Primary House's timeline

### Example: "Build User Dashboard"
```
Primary: Gryffindor (UI-heavy task)
Supporting:
  - Ravenclaw: API endpoints
  - Slytherin: Testing
  - Hufflepuff: Documentation

Coordination: Through contracts, not direct communication
```

---

## Agent Selection Within House

Once House is selected, choose specific agent:

### Selection Criteria (Priority Order)
1. **Best performer for task type** (from cache)
2. **Highest efficiency rating** (recent performance)
3. **Availability** (not currently assigned)
4. **Prefect status** (for Year 5+ tasks)

### Load Balancing
Don't overload top performers:
- Max 2 concurrent tasks per agent
- Distribute to build team capability
- Give newer agents mentored opportunities

---

## Sorting Hat Output

After making assignment, produce:

```markdown
## Sorting Hat Decision

**Task**: [Task description]
**Year Level**: [1/3/5/7]

### Assignment
**Primary House**: [House]
**Lead Agent**: [Agent]
**Supporting**: [House/Agent list or "None"]

### Rationale
- Domain match: [Why this House]
- Agent selection: [Why this Agent]
- Historical performance: [Relevant stats]

### Execution Mode
[Single/Order Delegation/Dumbledore's Army]

### Special Considerations
[Any relevant notes]
```

---

## Re-Sorting

Tasks can be re-sorted if:
- Original agent unavailable
- Scope changes significantly
- Agent requests help (outside expertise)
- Performance issues (Apparition Protocol)

Re-sorting requires Headmaster approval.

---

## Sorting Hat Cache Updates

After each mission completion:

1. Record task type and outcome
2. Update agent performance for that task type
3. Recalculate House specialization scores
4. Prune old data (keep last 20 per type)

### Cache Entry Format
```markdown
### Task Completion Record
**Task Type**: [Type]
**Agent**: [House]/[Agent]
**Quality**: [1-10]
**Efficiency**: [Multiplier]
**Date**: [YYYY-MM-DD]
```

---

## Edge Cases

### No Clear Domain Match
- Default to Ravenclaw (architecture/problem-solving)
- Or form exploratory team to assess

### All Agents Busy
- Queue task with priority
- Or escalate to Headmaster for resource allocation

### New Task Type
- Assign to House with closest domain match
- Mark as "learning" - reduced point penalty for inefficiency
- Create new cache entry after completion

### Conflict Between Houses
- Headmaster arbitrates
- Decision recorded in Pensieve
- Use outcome to update specialization scores

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kheery12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
