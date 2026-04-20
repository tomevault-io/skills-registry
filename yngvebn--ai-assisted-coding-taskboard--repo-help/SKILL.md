---
name: repo-help
description: Comprehensive guide to understanding and using this AI-assisted task management repository. Use this skill when users ask about how the repository works, what components are available, how to use agents/skills/prompts, or need guidance on the issue-tracking workflow. Use when this capability is needed.
metadata:
  author: yngvebn
---

# Repository Help Skill

This skill provides comprehensive guidance on understanding and using the AI-assisted task management system in this repository.

## Purpose

This repository provides a complete, file-based issue-tracking system designed for AI-assisted software development. It includes specialized agents, reusable skills, and automation scripts to manage the full lifecycle of issues from creation through completion.

## When to Use This Skill

Use this skill when users ask:
- "How does this repository work?"
- "What are agents/skills/prompts?"
- "How do I use the issue-tracking system?"
- "What commands/scripts are available?"
- "How do I report a bug or request a feature?"
- "How do I start working on tasks?"
- "What's the workflow for completing issues?"
- Any questions about repository structure, components, or workflows

## System Overview

### Core Concepts

**Issue-Tracking System**: File-based workflow for managing bugs, features, and refactors
- Issues are markdown files with YAML frontmatter metadata
- Issues move through folders: `backlog/` → `in-progress/` → `done/` → `archive/`
- PLANNING-BOARD.md maintains current priorities (3-5 items max)
- PowerShell scripts automate common operations

**Agents**: Specialized AI configurations for specific workflows
- **issue-reporter.agent.md** - Analyzes and documents bugs/features (investigation only, no implementation)
- **issue-tracking-expert.agent.md** - Expert on managing the issue-tracking system, knows all scripts and workflows

**Skills**: Reusable workflows that can be invoked with `/skill-name`
- **report-issue** - Create comprehensive issue documentation from user reports
- **start-working** - Pick up next priority and implement it following TDD workflow
- **skill-creator** - Guide for creating new skills

**Prompts**: Workflow templates for specific operations
- Located in `.github/prompts/*.prompt.md`
- Examples: start-working, reprioritize, split-epic, handoff, housecleaning

### Repository Structure

```
.
├── .claude/                       # Claude Code configuration
│   └── skills/                    # Reusable workflow skills
│       ├── report-issue/          # Bug/feature documentation skill
│       ├── start-working/         # Implementation workflow skill
│       ├── skill-creator/         # Skill creation guide
│       └── repo-help/             # This help skill
│
├── .github/
│   ├── agents/                    # Specialized AI agent configurations
│   │   ├── issue-reporter.agent.md         # Investigation & documentation
│   │   └── issue-tracking-expert.agent.md  # System management expert
│   │
│   └── prompts/                   # Workflow prompt templates
│       ├── start-working.prompt.md     # Begin implementation
│       ├── reprioritize.prompt.md      # Re-prioritize backlog
│       ├── split-epic.prompt.md        # Break down large issues
│       ├── handoff.prompt.md           # Prepare work handoff
│       └── housecleaning.prompt.md     # Maintenance tasks
│
├── issue-tracking/                # Issue management system
│   ├── AGENTS.md                  # **PRIMARY REFERENCE** - Complete workflow documentation
│   ├── PLANNING-BOARD.md          # Current priorities (3-5 items)
│   ├── backlog/                   # New issues
│   ├── in-progress/               # Active work
│   ├── done/                      # Recently completed
│   ├── archive/                   # Historical completions
│   └── wont-fix/                  # Rejected issues
│
└── scripts/                       # PowerShell automation scripts
    ├── issue-create.ps1           # Create new issue
    ├── issue-start.ps1            # Start working on issue
    ├── issue-complete.ps1         # Mark issue complete
    ├── backlog-view.ps1           # View/filter backlog
    └── archive-old-issues.ps1     # Archive old completions
```

## Key Documentation Files

### Essential Reading

1. **[issue-tracking/AGENTS.md](../../issue-tracking/AGENTS.md)** - **START HERE**
   - Complete workflow documentation
   - File naming conventions
   - Frontmatter specification
   - PowerShell script reference
   - Best practices and guidelines
   - Agent decision matrix
   - Risk assessment framework

2. **[.github/agents/issue-tracking-expert.agent.md](../../.github/agents/issue-tracking-expert.agent.md)**
   - Expert system for managing issue-tracking
   - Quick reference for scripts
   - Workflow examples
   - Troubleshooting guide

3. **[.github/agents/issue-reporter.agent.md](../../.github/agents/issue-reporter.agent.md)**
   - Investigation and documentation workflow
   - Root cause analysis methodology
   - Issue template structure

### Skill Documentation

- **[.claude/skills/report-issue/SKILL.md](../../.claude/skills/report-issue/SKILL.md)** - Bug/feature documentation skill
- **[.claude/skills/start-working/SKILL.md](../../.claude/skills/start-working/SKILL.md)** - Implementation workflow skill
- **[.claude/skills/skill-creator/SKILL.md](../../.claude/skills/skill-creator/SKILL.md)** - Creating new skills

## Common Workflows

### 1. Reporting a Bug or Requesting a Feature

**Use the report-issue skill:**
```
/report-issue
```

**What it does:**
1. Asks clarifying questions about the issue
2. Investigates the codebase thoroughly
3. Identifies root cause and affected components
4. Creates comprehensive issue file in `backlog/`
5. Sets priority, effort, and labels based on findings

**Manual alternative using scripts:**
```powershell
# Interactive mode
.\scripts\issue-create.ps1

# Non-interactive (for AI agents)
.\scripts\issue-create.ps1 -Type bug -Title "Timer not pausing" -Priority high -Effort 2h -NonInteractive
```

### 2. Starting Work on Next Priority

**Use the start-working skill:**
```
/start-working
```

**What it does:**
1. Checks PLANNING-BOARD.md for current priorities
2. Selects top unblocked item
3. Moves issue to in-progress/
4. Adds implementation plan
5. Follows TDD workflow (test first, then implement)
6. Updates progress in real-time
7. Moves to done/ when complete

**Manual alternative using scripts:**
```powershell
# View backlog priorities
.\scripts\backlog-view.ps1 -ShowStats -FilterPriority high

# Start working on specific issue
.\scripts\issue-start.ps1 FEATURE-issue-name

# Complete when done
.\scripts\issue-complete.ps1 FEATURE-issue-name
```

### 3. Viewing and Filtering Backlog

**View all issues with statistics:**
```powershell
.\scripts\backlog-view.ps1 -ShowStats
```

**Filter by priority:**
```powershell
.\scripts\backlog-view.ps1 -FilterPriority high
```

**Filter by type:**
```powershell
.\scripts\backlog-view.ps1 -FilterType bug
```

**Find quick wins (sorted by effort):**
```powershell
.\scripts\backlog-view.ps1 -SortBy effort
```

### 4. Managing PLANNING-BOARD

The PLANNING-BOARD.md file should always contain 3-5 current priorities in order of importance.

**Rules:**
- Maximum 3-5 items at any time
- Most important items at the top
- Remove completed items immediately
- Update status as work progresses
- Link to issue files in backlog/ or in-progress/

**When to update:**
- Before starting work (check priorities)
- During work (update status)
- After completion (remove item, add next)
- When priorities shift (reorder)

### 5. Archiving Old Completed Issues

Keep the `done/` folder lean (max ~20 items) by archiving older completions:

```powershell
# Archive issues completed more than 90 days ago
.\scripts\archive-old-issues.ps1

# Custom threshold (60 days)
.\scripts\archive-old-issues.ps1 -DaysOld 60

# Preview without moving
.\scripts\archive-old-issues.ps1 -WhatIf
```

## Issue Lifecycle

```
┌─────────────┐
│   backlog/  │  ← New issues created here
│  (created)  │
└──────┬──────┘
       │
       │ issue-start.ps1 or /start-working
       │
┌──────▼──────┐
│ in-progress/│  ← Active implementation
│  (working)  │  ← Update progress log in real-time
└──────┬──────┘
       │
       │ issue-complete.ps1
       │
┌──────▼──────┐
│    done/    │  ← Recently completed (~20 max)
│  (recent)   │
└──────┬──────┘
       │
       │ archive-old-issues.ps1 (>90 days)
       │
┌──────▼──────┐
│  archive/   │  ← Historical completions
│(historical) │
└─────────────┘
```

## Issue File Structure

All issues use YAML frontmatter for metadata:

```yaml
---
id: FEATURE-example              # Auto-generated from filename
type: feature                    # bug | feature | refactor | explore | plan
priority: high                   # high | medium | low
effort: 4h                       # 2h, 4h, 1d, 3d, 1w
status: backlog                  # backlog | in-progress | done | wont-fix
labels: [frontend, api]          # Tags for filtering
depends_on: []                   # Dependency issue IDs
blocks: []                       # Issues blocked by this
created: 2025-11-24             # YYYY-MM-DD
updated: 2025-11-24             # YYYY-MM-DD
started: null                    # Set when moved to in-progress
completed: null                  # Set when moved to done
---

# [Type]: [Short Description]

## Problem Statement
[Clear description of issue or feature need]

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Tests covering the feature

## Technical Context
[Root cause, affected components, file paths]

## Implementation Plan
[Added when work starts - approach, files to modify, tests needed]

## Progress Log
[Real-time updates during implementation]
- [Timestamp] - [What was done]

## Verification
- [ ] All acceptance criteria met
- [ ] All tests passing
- [ ] Documentation updated

## Resolution
[Final outcome when complete]
```

## File Naming Conventions

Use descriptive, kebab-case names with type prefix:

- **Bugs**: `BUG-timer-not-pausing.md`
- **Features**: `FEATURE-export-results.md`
- **Refactors**: `REFACTOR-consolidate-auth-logic.md`
- **Explorations**: `EXPLORE-database-options.md`
- **Plans** (multi-phase): `PLAN-authentication-phase1.md`

## Development Principles

### Always Follow TDD (Test-Driven Development)

1. **Check existing test coverage** for affected code
2. **Write failing test** that reproduces bug or validates feature (RED)
3. **Fix implementation** to make test pass (GREEN)
4. **Monitor tests continuously** during implementation
5. **Add integration/E2E tests** as needed

### Real-Time Progress Tracking

Update the issue file's Progress Log frequently:

```markdown
## Progress Log
- 2025-11-19 14:30 - Started implementation, reviewed existing tests
- 2025-11-19 14:45 - Added failing test in SessionController.test.ts
- 2025-11-19 15:00 - Implemented fix in SessionController.ts:142
- 2025-11-19 15:15 - All tests passing
- 2025-11-19 15:30 - Added E2E test for full user flow
```

### Analysis Before Action

Before creating an issue:
1. Research thoroughly using search tools
2. Identify root cause with specific file paths and line numbers
3. Understand affected components and dependencies
4. Check test coverage
5. Ask clarifying questions if unclear

## Agent Decision Matrix

Choose the right workflow for the task:

| Issue Characteristics | Agent/Skill | File Prefix |
|-----------------------|-------------|-------------|
| Bug fix, < 1 day | report-issue → start-working | `BUG-` |
| Small feature, < 1 week | report-issue → start-working | `FEATURE-` |
| Refactor, < 1 week | report-issue → start-working | `REFACTOR-` |
| Research spike | report-issue → start-working | `EXPLORE-` |
| Major feature, > 1 week | Split into phases | `PLAN-` + `EPIC-` |

## PowerShell Scripts Quick Reference

| Script | Purpose | Common Usage |
|--------|---------|--------------|
| `backlog-view.ps1` | View & filter issues | `.\scripts\backlog-view.ps1 -ShowStats` |
| `issue-create.ps1` | Create new issue | `.\scripts\issue-create.ps1 -Type bug -Title "..." -NonInteractive` |
| `issue-start.ps1` | Start working | `.\scripts\issue-start.ps1 FEATURE-name` |
| `issue-complete.ps1` | Mark done | `.\scripts\issue-complete.ps1 FEATURE-name` |
| `archive-old-issues.ps1` | Archive old items | `.\scripts\archive-old-issues.ps1` |

## Best Practices

1. **Analysis first**: Research thoroughly before creating issues with specific file paths and line numbers
2. **TDD always**: Write test first (red), then implement (green), monitor continuously
3. **Real-time updates**: Timestamp every step in Progress Log during implementation
4. **One thing at a time**: Limit work in in-progress/ to 1-2 items
5. **Monitor tests continuously**: Keep test suite running; tests must stay green
6. **Be specific**: Include file paths, line numbers, and actionable descriptions
7. **Document decisions**: Record WHY in Progress Log, not just WHAT
8. **Complete fully**: Don't move to done until ALL verification criteria met

## Risk Assessment

For production-critical changes, issues include risk assessment:

### Risk Categories
- `security` - Auth, data exposure, vulnerabilities
- `data-loss` - Could cause data deletion/corruption
- `performance` - Could degrade performance
- `breaking-change` - API/backward compatibility issues
- `integration` - Third-party dependencies
- `database` - Schema changes, migrations
- `compliance` - GDPR, legal requirements
- `none` - Low-risk (docs, CSS, tests only)

### Risk Impact Levels
- `high` - Data loss, security breach, downtime, revenue loss
- `medium` - Feature degradation, poor UX, performance issues
- `low` - Minor bugs, cosmetic issues, easy to rollback

**High-risk issues require**:
- Security review
- Staging validation
- Rollback procedure
- Monitoring setup
- Load testing (if performance-critical)

## Troubleshooting

### Issue not found
Check filename spelling and verify correct folder (backlog/in-progress/done).

### PLANNING-BOARD is empty
Use the reprioritize prompt to populate it from backlog.

### Circular dependency detected
Review `depends_on` and `blocks` fields to eliminate loops.

### Tests failing before completion
Never mark issue as done with failing tests. Fix regressions immediately.

## Getting Started

**New users should:**

1. Read [issue-tracking/AGENTS.md](../../issue-tracking/AGENTS.md) - Complete system documentation
2. Check [issue-tracking/PLANNING-BOARD.md](../../issue-tracking/PLANNING-BOARD.md) - Current priorities
3. Run `.\scripts\backlog-view.ps1 -ShowStats` - See what's in the backlog
4. Use `/report-issue` to report bugs or request features
5. Use `/start-working` to begin implementation work

## Additional Resources

### For Creating Issues
- [.github/agents/issue-reporter.agent.md](../../.github/agents/issue-reporter.agent.md) - Investigation methodology
- [.claude/skills/report-issue/SKILL.md](../../.claude/skills/report-issue/SKILL.md) - Issue documentation skill

### For Implementation
- [.claude/skills/start-working/SKILL.md](../../.claude/skills/start-working/SKILL.md) - Implementation workflow
- [.github/prompts/start-working.prompt.md](../../.github/prompts/start-working.prompt.md) - Start work prompt

### For System Management
- [.github/agents/issue-tracking-expert.agent.md](../../.github/agents/issue-tracking-expert.agent.md) - System expert
- [.github/prompts/reprioritize.prompt.md](../../.github/prompts/reprioritize.prompt.md) - Backlog prioritization
- [.github/prompts/split-epic.prompt.md](../../.github/prompts/split-epic.prompt.md) - Breaking down large issues

### For Extending the System
- [.claude/skills/skill-creator/SKILL.md](../../.claude/skills/skill-creator/SKILL.md) - Creating new skills

## Summary

This repository provides a complete AI-assisted task management system with:
- **File-based issue tracking** with automated scripts
- **Specialized agents** for investigation and management
- **Reusable skills** for common workflows
- **TDD-first approach** with continuous testing
- **Real-time progress tracking** throughout implementation
- **Risk assessment framework** for production safety

The system is designed to be simple, transparent, and optimized for AI collaboration while remaining fully accessible to human developers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yngvebn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
