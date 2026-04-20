---
name: vibe-coding-workflow
description: Complete 6A workflow methodology (Align → Architect → Atomize → Approve → Automate → Assess) for AI-assisted development. Use when starting new features, planning projects, managing development phases, or when asked about workflow, planning, architecture, task breakdown, or project execution methodology. Use when this capability is needed.
metadata:
  author: hebertzhu
---

# Vibe Coding Workflow

Systematic 6-phase workflow for AI-assisted development that ensures clarity, quality, and maintainability.

## Quick Start

When starting any development task, follow the 6A workflow:

```
Align → Architect → Atomize → Approve → Automate → Assess
```

Never skip phases unless explicitly justified.

## Core Principles

1. **Planning First** - Plan before executing
2. **Documentation as Truth** - Documentation is the single source of truth
3. **Glue Coding** - Reuse > Connect > Create

## The 6A Workflow

### 1️⃣ Align (对齐)

**Goal**: Eliminate ambiguity, establish consensus

**Required Outputs**:
- `ALIGNMENT_*.md` - Requirements understanding and boundary definition
- `CONSENSUS_*.md` - Confirmed consensus points

**Checklist**:
- [ ] Analyze existing project context
- [ ] Clearly define requirement scope
- [ ] Identify assumptions and constraints
- [ ] List all ambiguities
- [ ] Get user confirmation

**Exit Condition**: User confirms "Requirements confirmed" or "Aligned"

### 2️⃣ Architect (架构)

**Goal**: Design system structure

**Required Output**:
- `DESIGN_*.md` - Architecture design document

**Design Content**:
- Overall architecture diagram (Mermaid)
- Module responsibility division
- Interface and contract definitions
- Data flow
- Error handling strategy

**Principles**:
- Prioritize reusing existing architecture
- Avoid over-design
- Keep it simple and direct

**Exit Condition**: User confirms "Design confirmed" or "Designed"

### 3️⃣ Atomize (原子化)

**Goal**: Break down into executable atomic tasks

**Required Output**:
- `TASK_*.md` - Task breakdown document

**Task Definition**:
```yaml
Task-ID: T001
Name: Task name
Input: Input conditions
Output: Output results
Dependencies: [T000]
Estimate: Time estimate
Acceptance: Acceptance criteria
```

**Principles**:
- Each task independently verifiable
- Clear dependencies
- Generate dependency graph

**Exit Condition**: Task list complete and dependencies clear

### 4️⃣ Approve (批准)

**Goal**: Freeze task list

**Checklist**:
- [ ] Task completeness verification
- [ ] Feasibility assessment
- [ ] Testability confirmation
- [ ] User final approval

**Exit Condition**: User confirms "Start execution" or "Approved"

### 5️⃣ Automate (自动化)

**Goal**: Execute tasks in order

**Execution Loop**:
```
Verify Input → Implement → Test → Update Docs → Mark Complete
```

**Rules**:
- Strictly follow dependency order
- Execute one task at a time
- Follow existing code style
- Use environment variables for sensitive info

**When Problems Occur**: Immediately STOP, report and wait for instructions

### 6️⃣ Assess (评估)

**Goal**: Acceptance and delivery

**Required Outputs**:
- `ACCEPTANCE_*.md` - Acceptance report
- `FINAL_*.md` - Final summary
- `TODO_*.md` - Remaining items

**Checklist**:
- [ ] All requirements met
- [ ] All tests pass
- [ ] Build successful
- [ ] Documentation updated
- [ ] No undocumented behavior

## Fast Track

### Small Tasks (< 10 lines of code)
Execute directly, skip full workflow

### Bug Fixes
1. Confirm problem → 2. Locate cause → 3. Minimal fix → 4. Verify

### Emergency Fixes
When user explicitly says "urgent", can simplify workflow but must document afterwards

## Best Practices

### DO
✅ Always start with Align phase for new features
✅ Document all decisions and assumptions
✅ Get explicit user confirmation at each phase
✅ Keep tasks atomic and independently testable
✅ Follow the workflow order strictly

### DON'T
❌ Skip phases without justification
❌ Guess user intent
❌ Silently expand scope
❌ Jump to coding without planning
❌ Ignore existing documentation

## Integration with Other Practices

- **Testing**: Follow TDD methodology during Automate phase
- **Code Quality**: Apply code review standards before marking tasks complete
- **Git Workflow**: Use proper commit messages and PR process
- **Performance**: Consider performance implications during Architect phase

## Troubleshooting

### Workflow Feels Too Heavy
- Use Fast Track for truly small tasks
- Combine phases for minor changes
- Document the simplification decision

### User Unclear on Requirements
- Stay in Align phase longer
- Ask clarifying questions
- Provide concrete examples
- Don't proceed until clarity achieved

### Tasks Too Large
- Return to Atomize phase
- Break down further
- Ensure each task < 1 day of work

### Scope Creep
- Reference CONSENSUS document
- Explicitly call out scope changes
- Get user approval for expansions

## Related Documentation

For deeper understanding of specific phases:
- **Planning**: See `references/planning-guide.md`
- **Architecture**: See `references/architecture-patterns.md`
- **Task Breakdown**: See `references/task-decomposition.md`

---

*Remember: The workflow exists to ensure quality and maintainability. Follow it, and your projects will thank you.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hebertzhu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
