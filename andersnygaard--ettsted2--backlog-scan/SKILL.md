---
name: backlog-scan
description: Bulk backlog scanner that analyzes the entire finans codebase vs CLAUDE.md, identifies ALL gaps, and generates a comprehensive, prioritized, numbered task backlog. Uses broad→narrow pattern. Use when this capability is needed.
metadata:
  author: andersnygaard
---

# Backlog Scan

Autonomous bulk scanner for comprehensive backlog generation. Scans entire codebase against CLAUDE.md spec, finds ALL gaps, generates numbered tasks.

**Pattern**: Broad→Narrow (scan everything, then focus on gaps)

**🚨 PLANNING ONLY - NO IMPLEMENTATION**
This skill creates task files. It does NOT write code or start implementation.

**Use `/new-task` instead** for a single specific task (narrow→broad pattern).

## When to Use This Skill

**Use this skill when**:
- Starting a new development phase - need comprehensive backlog
- Backlog is stale or empty
- Want to discover ALL missing features, refactors, improvements
- Need to organize and prioritize existing unnumbered tasks
- Running `/discover-tasks` slash command

**DO NOT use this skill for**:
- Creating a single specific task → use `feature-planning` skill
- Implementing tasks → this skill only discovers and plans
- Quick bug fixes → just fix them directly

## Context Loading (ALWAYS FIRST)

Before any analysis, load ALL context:

```
┌─────────────────────────────────┐
│        CONTEXT LOADING          │
│  • ALL rules from .claude/rules/│
│  • ALL done/ tasks (recent 30)  │
│  • ALL available skills         │
│  • CLAUDE.md completely         │
└─────────────────────────────────┘
```

1. **Load ALL rules from `.claude/rules/`**
   - List: `find .claude/rules -name "*.md"`
   - Read rules for ALL areas (frontend/, backend/, components/, e2e/)
   - Rules contain patterns, gotchas, decisions

2. **Check ALL completed tasks in `.task-board/done/`**
   - Scan recent 20-30 completed tasks
   - Learn task format and quality standards
   - Identify recurring themes

3. **Note ALL available skills**
   - `frontend-design` - UI/CSS implementations
   - `node-backend` - API routes
   - `rule-making-skill` - Create missing rules
   - `storybook-stories` - Component documentation
   - `task-board` - Detailed task planning

## Scan Workflow

### Phase 1: Full Codebase Analysis

**Goal**: Understand EVERYTHING that exists

1. **Read CLAUDE.md completely**
   - ALL described features, architecture, requirements
   - Technology stack and patterns
   - Finans domain (portfolio tracking, F.I.R.E., calculators)

2. **Scan ALL codebase areas**
   - `/frontend/src/features/` - existing features
   - `/backend/src/routes/` - API routes
   - `/components/src/` - component library
   - `/e2e/` - test coverage
   - TODOs, FIXMEs in code

3. **Review ALL existing tasks**
   - `.task-board/backlog/`
   - `.task-board/in-progress/`
   - `.task-board/on-hold/`
   - Identify gaps, duplicates, vague tasks

### Phase 2: Gap Analysis

**Goal**: Find ALL gaps between spec and implementation

- Features in CLAUDE.md NOT yet implemented
- Missing infrastructure (CI/CD, monitoring)
- Code quality issues (validation, error handling)
- Technical debt (deprecated patterns, TODOs)
- Finans-specific gaps (Norwegian formatting, calculators)

### Phase 3: Task Generation

**Quality Bar** (ALL must be met):
- ✅ Clear value: Obvious user benefit OR technical necessity
- ✅ Well-scoped: Not epic-sized, not trivial
- ✅ Actionable: Can implement without major unknowns
- ✅ Domain-aligned: Fits finans portfolio tracking
- ✅ Non-redundant: Not covered by existing task

**For each gap that meets quality bar**:
1. Invoke `task-board` skill for detailed planning
2. Provide context and priority
3. Review output, assign number

### Phase 4: Numbering & Organization

**🚨 CRITICAL**: Scan ALL folders for highest number:

```
1. Glob: .task-board/**/*.md
2. Scan: backlog/ + in-progress/ + done/
3. Find highest number
4. Next task = highest + 1
```

**Never reuse numbers** - done/ tasks are immutable history.

**File format**: `NNN-TYPE-description.md`
- `076-FEATURE-portfolio-dashboard.md`
- `077-REFACTOR-validation-consolidation.md`

### Phase 5: Cleanup

- Enhance vague existing tasks
- Merge duplicates
- Remove outdated tasks
- Final state: numbered, no duplicates, quality bar met

## Output

Summary report with:
- Tasks created/enhanced/merged
- Breakdown by type and priority
- Top 5 priorities
- Next steps

## Delegates To

- `task-board` skill - for detailed plan files

## See Also

- `feature-planning` skill - for single task (narrow→broad)
- `.task-board/WORKFLOW.md` - task management workflow
- `.claude/CLAUDE.md` - project instructions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andersnygaard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
