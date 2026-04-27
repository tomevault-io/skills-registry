---
name: moai-alfred-workflow
description: Guide 4-step workflow execution with task tracking and quality gates Use when this capability is needed.
metadata:
  author: kivo360
---

# Alfred 4-Step Workflow Guide

## Skill Metadata

| Field | Value |
| ----- | ----- |
| **Skill Name** | moai-alfred-workflow |
| **Version** | 1.0.0 (2025-11-02) |
| **Status** | Active |
| **Tier** | Alfred |
| **Purpose** | Guide systematic 4-step workflow execution |

---

## What It Does

Alfred uses a consistent 4-step workflow for all user requests to ensure clarity, planning, transparency, and traceability.

**Key capabilities**:
- ✅ Intent clarification with questions
- ✅ Task planning and decomposition
- ✅ Transparent progress tracking with TodoWrite
- ✅ Automated reporting and commits
- ✅ Quality gate validation

---

## When to Use

**Automatic triggers**:
- User request received → analyze intent
- Multiple interpretation possible → use AskUserQuestion
- Task complexity > 1 step → invoke Plan Agent
- Executing tasks → activate TodoWrite tracking
- Task completion → generate report

**Manual reference**:
- Understanding workflow execution
- Planning multi-step features
- Learning best practices for task tracking

---

## The 4-Step Workflow

### Step 1: Intent Understanding

**Goal**: Clarify user intent before any action

**Actions**:
- Evaluate request clarity
  - HIGH clarity → Skip to Step 2 directly
  - MEDIUM/LOW clarity → Invoke AskUserQuestion
- Present 3-5 clear options (not open-ended)
- Gather user responses before proceeding

**When to Ask Questions**:
- Multiple tech stack choices available
- Architecture decisions needed
- Business/UX decisions unclear
- Ambiguous requirements
- Existing component impacts unknown

**Example**:
```
User says: "Add authentication"
          ↓
Clarity = MEDIUM (multiple approaches possible)
          ↓
Ask: "Which authentication method?"
- Option 1: JWT tokens
- Option 2: OAuth 2.0
- Option 3: Session-based
```

---

### Step 2: Plan Creation

**Goal**: Analyze tasks and identify execution strategy

**Actions**:
- Invoke Plan Agent (built-in Claude agent) to:
  - Decompose tasks into structured steps
  - Identify dependencies between tasks
  - Determine single vs parallel execution
  - Estimate file changes and scope
- Output structured task breakdown

**Plan Output Format**:
```
Task Breakdown:

Phase 1: Preparation (30 mins)
├─ Task 1: Set up environment
├─ Task 2: Install dependencies
└─ Task 3: Create test fixtures

Phase 2: Implementation (2 hours)
├─ Task 4: Core feature (parallel ready)
├─ Task 5: API endpoints (parallel ready)
└─ Task 6: Tests (depends on 4, 5)

Phase 3: Verification (30 mins)
├─ Task 7: Integration testing
├─ Task 8: Documentation
└─ Task 9: Code review
```

---

### Step 3: Task Execution

**Goal**: Execute tasks with transparent progress tracking

**Actions**:
1. Initialize TodoWrite with all tasks (status: pending)
2. For each task:
   - Update TodoWrite: pending → **in_progress**
   - Execute task (call appropriate sub-agent or action)
   - Update TodoWrite: in_progress → **completed**
3. Handle blockers: Keep in_progress, create new blocking task

**TodoWrite Rules**:
- Each task must have:
  - `content`: Imperative form ("Run tests", "Fix bug")
  - `activeForm`: Present continuous ("Running tests", "Fixing bug")
  - `status`: One of pending/in_progress/completed
- **EXACTLY ONE** task in_progress at a time (unless Plan Agent approved parallel)
- Mark completed ONLY when fully done (tests pass, no errors, implementation complete)

**Example TodoWrite Progression**:

Initial state (all pending):
```
1. [pending] Set up environment
2. [pending] Install dependencies
3. [pending] Implement core feature
4. [pending] Write tests
5. [pending] Documentation
```

After starting Task 1:
```
1. [in_progress] Set up environment     ← ONE task in progress
2. [pending] Install dependencies
3. [pending] Implement core feature
4. [pending] Write tests
5. [pending] Documentation
```

After completing Task 1 and starting Task 2:
```
1. [completed] Set up environment      ✅
2. [in_progress] Install dependencies  ← NOW in progress
3. [pending] Implement core feature
4. [pending] Write tests
5. [pending] Documentation
```

**Handling Blockers**:

If blocked during execution:
```
Example: Task 4 blocked by missing library

Action:
├─ Keep Task 4 as in_progress
├─ Create new task: "Install library X"
├─ Add to todo list
└─ Start new task first
```

---

### Step 4: Report & Commit

**Goal**: Document work and create git history

**Actions**:
- **Report Generation**: ONLY if user explicitly requested
  - ❌ Don't auto-generate in project root
  - ✅ OK to generate in `.moai/docs/`, `.moai/reports/`, `.moai/analysis/`
- **Git Commit**: ALWAYS create commits (mandatory)
  - Call git-manager for all Git operations
  - TDD commits: RED → GREEN → REFACTOR
  - Include Alfred co-authorship

**Report Conditions**:

```
User says: "Show me a report"
         ↓
Generate report → .moai/reports/task-completion-001.md

User says: "I'm done with feature X"
         ↓
NO auto-report → just create commit
(Only create report if explicitly requested)
```

**Commit Message Format**:
```
feat: Add authentication support

- JWT token validation implemented
- Session management added
- Rate limiting configured

🎩 Generated with Claude Code

Co-Authored-By: 🎩 Alfred@[MoAI](https://adk.mo.ai.kr)
```

---

## Workflow Validation Checklist

Before considering workflow complete:
- ✅ All steps followed in order (Intent → Plan → Execute → Commit)
- ✅ No assumptions made (AskUserQuestion used when unclear)
- ✅ TodoWrite tracks all tasks
- ✅ Reports only generated on explicit request
- ✅ Commits created for all completed work
- ✅ Quality gates passed (tests, linting, type checking)

---

## Decision Trees

### When to Use AskUserQuestion

```
Request clarity unclear?
├─ YES → Use AskUserQuestion
│   ├─ Present 3-5 clear options
│   ├─ Use structured format
│   └─ Wait for user response
└─ NO → Proceed to planning
```

### When to Mark Task Completed

```
Task marked in_progress?
├─ Code implemented → tests pass?
├─ Tests pass → type checking pass?
├─ Type checking pass → linting pass?
└─ All pass → Mark COMPLETED ✅
   └─ NOT complete → Keep in_progress ⏳
```

### When to Create Blocking Task

```
Task execution blocked?
├─ External dependency missing?
├─ Pre-requisite not done?
├─ Unknown issue?
└─ YES → Create blocking task
   └─ Add to todo list
   └─ Execute blocking task first
   └─ Return to original task
```

---

## Key Principles

1. **Clarity First**: Never assume intent
2. **Systematic**: Follow 4 steps in order
3. **Transparent**: Track all progress visually
4. **Traceable**: Document every decision
5. **Quality**: Validate before completion

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kivo360) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
