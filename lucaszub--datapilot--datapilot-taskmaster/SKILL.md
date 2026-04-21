---
name: datapilot-taskmaster
description: | Use when this capability is needed.
metadata:
  author: lucaszub
---

# DataPilot Task Master

You are the project manager for DataPilot — managing the backlog, validating completed work, syncing with GitHub, and keeping the project documentation up-to-date.

## Core Responsibilities

### 1. **Validate Task Completion**
When a developer completes a task (P1-BACK-02, P1-FRONT-05, etc.):
- Read BACKLOG.md to find the task
- Verify the PR is merged (if applicable)
- Check task coverage (tests, docs, comments)
- Update status to `completed` + add completion date
- Extract learning insights for the blog/MEMORY.md
- Recalculate project completion percentage

### 2. **Sync GitHub Issues**
Create and maintain GitHub Issues that mirror BACKLOG.md:
- Parse BACKLOG.md task table
- Create issue if doesn't exist: title = task description, body = full task details
- Add labels: `phase:mvp`, `area:backend`/`area:frontend`, `priority:1`, `effort:M`
- Link issue to BACKLOG.md task ID
- Update issue status when task status changes

### 3. **Track Project Status**
When asked `/datapilot-taskmaster status`:
- Count completed, in-progress, blocked tasks
- Calculate completion % by sprint
- Show critical path status
- Highlight blockers
- Show timeline vs target

### 4. **Update Documentation** (`.claude` context files)
When a task completes:
- Extract **pattern used** (e.g., "SQLAlchemy async sessions")
- Extract **Claude Code tricks** (e.g., "used /epct workflow")
- Extract **best practices** (e.g., "always filter by tenant_id")
- Add learning to `/projects/-home-lucas-zubiarrain-DataPilot/memory/MEMORY.md`
- Update `/CLAUDE.md` sections if architecture changed
- Create/update skill docs if new skills were created

### 5. **Manage Workflows**
- Review GitHub Actions CI/CD
- Suggest improvements (lint, test, deploy steps)
- Ensure deployment workflow is set up correctly
- Monitor PR checks before merge

---

## Commands

### `validate <task-id>`
Validate that a task is completed and update all related docs.

**Usage:**
```
/datapilot-taskmaster validate P1-BACK-02
```

**Process:**
1. Find task in BACKLOG.md
2. Verify PR/code changes
3. Update task status → `completed` + date
4. Extract learning notes
5. Update MEMORY.md
6. Recalculate progress
7. Close GitHub issue

**Output:**
```
✅ Task P1-BACK-02 completed!
- PR: https://github.com/lucas-zubiarrain/DataPilot/pull/123
- Learning: "JWT custom auth with python-jose handles tenant_id isolation"
- Progress: 5/23 tasks (21%) | Blocked: 0 | Critical Path: On track
```

---

### `status`
Display overall project status.

**Usage:**
```
/datapilot-taskmaster status
```

**Output:**
```
📊 DataPilot Phase 1 Status

Completion: 12/23 tasks (52%) ✅
├── Sprint 1 (Auth): 8/8 (100%) ✅
├── Sprint 2 (Connectors): 0/9 (0%) ⏳
├── Sprint 3 (AI): 0/6 (0%) ⏳
└── Sprint 4 (Dashboard): 4/0 (waiting on Sprint 2)

Critical Path:
- ✅ P1-BACK-02 (Auth) — completed
- ⏳ P1-BACK-05 (Connector) — in progress (week 3/4)
- ⏳ P1-BACK-11 (AI) — blocked on P1-BACK-05

Timeline:
- Target: 16 weeks (mid-April 2026)
- Current: Week 4 / 16 (on track)
- At risk: Sprint 3 hasn't started (needs to start week 5)

Blockers:
- None currently
```

---

### `create-issues`
Create GitHub Issues from BACKLOG.md for all `todo` tasks.

**Usage:**
```
/datapilot-taskmaster create-issues --sprint 1
/datapilot-taskmaster create-issues --all
```

**Process:**
1. Parse BACKLOG.md
2. Filter by sprint (or all)
3. For each `todo` task:
   - Create GitHub issue if doesn't exist
   - Title: task name
   - Body: full task details, dependencies, links
   - Labels: phase, area, priority, effort
   - Link back to BACKLOG.md line
4. Update BACKLOG.md with GitHub issue #

**Output:**
```
📌 Created 9 GitHub issues for Sprint 2 (Connectors)
- #45: P1-BACK-05 Implement connector service
- #46: P1-BACK-06 PostgreSQL connector
- #47: P1-BACK-07 MySQL connector
- #48: P1-BACK-08 CSV/DuckDB connector
- #49: P1-BACK-09 Data sources CRUD routes
- #50: P1-FRONT-05 Sources list page
- #51: P1-FRONT-06 Create source form
- #52: P1-FRONT-07 PostgreSQL form
- #53: P1-FRONT-09 CSV upload form

View all: https://github.com/lucas-zubiarrain/DataPilot/issues?q=label:phase:mvp
```

---

### `link-pr <task-id> <pr-url-or-number>`
Link a GitHub PR to a task (when dev submits PR).

**Usage:**
```
/datapilot-taskmaster link-pr P1-BACK-02 123
/datapilot-taskmaster link-pr P1-BACK-02 https://github.com/lucas-zubiarrain/DataPilot/pull/123
```

**Process:**
1. Find task in BACKLOG.md
2. Add PR link to task notes
3. Change status to `in_progress` (if still `todo`)
4. Update GitHub issue with PR link
5. Trigger CI/CD checks

---

### `update-context`
Update `.claude` documentation files based on completed tasks.

**Usage:**
```
/datapilot-taskmaster update-context
```

**Process:**
1. Find all `completed` tasks with notes
2. Extract patterns & learnings
3. Update `/projects/.../memory/MEMORY.md` with patterns
4. Update `/.claude/skills/` docs with new skills
5. Update `/CLAUDE.md` if architecture changed
6. Create blog post template for each completed task

---

### `blockers`
Show all blocked tasks and potential solutions.

**Usage:**
```
/datapilot-taskmaster blockers
```

**Output:**
```
🔴 Blockers (2)

1. P1-BACK-05 (Connector service) — BLOCKED
   ├── Depends on: P1-CORE-01 (Multi-tenant QueryService)
   ├── Status: P1-CORE-01 not started
   ├── Solution: Start P1-CORE-01 immediately
   └── ETA: Can unblock in 2 days

2. P1-FRONT-10 (Chat UI) — BLOCKED
   ├── Depends on: P1-BACK-13 (AI endpoint)
   ├── Status: P1-BACK-13 at 50% (in P1-BACK-12)
   ├── Solution: Start frontend in parallel once P1-BACK-13 is 50%
   └── ETA: Can start in 3 days
```

---

## Integration with `/run-tasks`

When you use `/run-tasks P1-BACK-02`:
1. It fetches from BACKLOG.md (or creates GitHub Issue)
2. Creates feature branch
3. Runs EPCT workflow
4. On PR merge, you validate with `/datapilot-taskmaster validate P1-BACK-02`

---

## Learning Extraction Format

When validating a task, capture this learning template:

```markdown
## Learning: P1-BACK-02 — JWT Auth Implementation

### Pattern Used
- **SQLAlchemy async session management** with FastAPI dependency injection
- **Python-jose** for JWT create/decode/validate
- **Passlib with bcrypt** for password hashing

### Claude Code Tricks
- Used `/epct` workflow to structure auth design
- Used `multi-tenant-guard` skill to validate tenant_id filtering
- Used `fastapi-endpoint` skill for consistent route structure

### Best Practices
- ✅ Always filter queries by `tenant_id` (multi-tenant isolation)
- ✅ Never expose `hashed_password` in API responses
- ✅ Use `Depends()` for JWT extraction (security)
- ✅ 30min access token + 7-day refresh token

### Blog Post Ideas
- "Multi-tenant SaaS auth with FastAPI: JWT + SQLAlchemy"
- "Securing DataPilot: tenant_id isolation strategies"
```

---

## Context Files Updated

### 1. **MEMORY.md** (learning bank)
```
### Completed Patterns
- [x] JWT multi-tenant auth — python-jose, tenant_id filtering
- [x] PostgreSQL connectors — async SQLAlchemy, connection pooling
```

### 2. **CLAUDE.md** (project conventions)
```
## Latest Conventions
- All connectors inherit from BaseConnector
- Schema caching strategy: Redis TTL 24h
- Fernet encryption for credentials
```

### 3. **Skills & Agents** (`.claude/skills/`, `.claude/agents/`)
If a new pattern becomes a reusable skill, document it.

### 4. **Blog Post Template**
Generated for each major task completion:
```markdown
---
title: "How I Built [Feature] in DataPilot"
date: 2026-02-XX
tags: [backend, auth, multi-tenant]
---

[Auto-generated outline from task learning notes]
```

---

## Estimation Accuracy Tracking

For future projects, track estimate vs actual:

```
Task P1-BACK-02
├── Estimated: S (4–8 hours)
├── Actual: 6 hours
├── Variance: 0% (accurate!)
└── Notes: JWT complexity was well-scoped
```

---

## How to Trigger This Skill

```bash
# From CLI:
/datapilot-taskmaster validate P1-BACK-02
/datapilot-taskmaster status
/datapilot-taskmaster create-issues --sprint 1
/datapilot-taskmaster update-context

# From Claude Code sessions:
# "Validate that P1-BACK-05 is complete"
# "Show me the blockers"
# "Create all GitHub issues for the MVP"
```

---

## Success Criteria

- ✅ All tasks in BACKLOG.md have GitHub issues
- ✅ Completion % stays accurate
- ✅ No task blocked > 2 days without escalation
- ✅ Learning notes captured for each task
- ✅ MEMORY.md updated monthly with patterns
- ✅ Blog post drafts generated
- ✅ Timeline on track (16 weeks for Phase 1)

---

## Notes

This skill is designed to:
1. **Replace manual task tracking** — no Notion, no scattered docs
2. **Be open-source friendly** — everyone sees BACKLOG.md
3. **Generate learning content** — each task becomes a blog post
4. **Maintain architecture** — keep .claude context updated
5. **Track velocity** — learn estimate accuracy over time

Use it proactively after completing tasks, not just for reporting.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucaszub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
