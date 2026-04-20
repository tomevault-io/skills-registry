---
name: cctdspec
description: Interactive planning skill. Brainstorm requirements with user, create stories, break down tasks, and write SDD specs. Supports full flow from scratch, partial flow from existing story, or single task spec writing. Designed for teammate dispatch in Phase 4. Use when this capability is needed.
metadata:
  author: esakat
---

# CCTD Spec

Interactive planning: discovery → story → task breakdown → SDD specs.
Read `{SKILL_DIR}/../_shared/format.md` for file format specs.

## Routing

Parse $ARGUMENTS to determine entry point:

| Input | Entry Point | Action |
|---|---|---|
| (empty) or free text | Phase 1 | Full flow: discovery → story → tasks → specs |
| Story ID (S001) | Phase 3 | Task breakdown + spec writing for existing story |
| Task ID (S001-001) | Phase 4 | Write/refine spec for single task |

## Phase 1: Discovery (Sync)

Socratic dialogue to uncover requirements. User interaction required.

1. Ask probing questions (max 3-4 per round):
   - What problem does this solve?
   - Who are the target users?
   - Scope and constraints?
   - Integration points?
   - Non-functional requirements?
2. Summarize findings after each round
3. Continue until requirements are clear enough for a User Story
4. Transition to Phase 2 when user confirms

## Phase 2: Story Creation (Sync)

User approval required.

1. Draft User Story:
   - Title
   - User Story (As a... I want... So that...)
   - Acceptance Criteria (testable, specific)
   - Labels, Priority
2. Present to user for approval
3. On approval: create `.tasks/stories/{ID}.md` and update `index.md`
4. Display: `✅ {ID} \`{title}\` を作成 (DEFINED)`
5. Proceed to Phase 3

## Phase 3: Task Breakdown (Semi-sync)

Present plan, user approves before creation.

1. Analyze the story's requirements and AC
2. Break down into implementation tasks. For each:
   - Title (specific, actionable)
   - Agent type (see format.md)
   - Model: `opus` (default) or `sonnet` (simple/routine only)
   - Dependencies between tasks
   - Priority
3. Present task breakdown:
   ```
   | # | Title | Agent | Model | Deps | Rationale |
   ```
4. Model selection rationale:
   - `opus`: complex design, architecture, multi-file, security-sensitive
   - `sonnet`: config, type definitions, boilerplate, simple single-file changes
5. On approval: create `.tasks/tasks/{ID}.md` for each, update `index.md`
6. Proceed to Phase 4

## Phase 4: SDD Spec Writing (Async)

Claude autonomous. Ask user only when multiple valid approaches exist.

For each task in DEFINED status under the story:

1. Read the story context and task metadata
2. Write the Spec section:
   - What to implement
   - Technical approach / constraints
   - Input/output expectations
   - Files to create or modify
   - Acceptance criteria for this task
3. If multiple valid approaches → **ask user to choose**
4. Run AI_READY checklist (see format.md)
5. If all checks pass → update status to AI_READY
6. Log: `[{date}] Spec作成完了 → AI_READY`

### Teammate Dispatch

Phase 4 can be parallelized via teammates:
- Each task's spec writing is independent (unless shared design decisions)
- Team Manager dispatches per `workflow.md` rules
- Teammate receives: story context + task metadata + instruction to write Spec
- Teammate writes Spec, runs AI_READY checklist, updates status

## Single Task Entry (via Task ID)

When $ARGUMENTS is a task ID (e.g., S001-001):
1. Read the task file and its parent story
2. Execute Phase 4 for this single task
3. If spec already exists, refine/update based on additional context from $ARGUMENTS

## Post-Completion Guide

After Phase 4 completes (all tasks AI_READY), display:

```
✅ 全タスクのSDD仕様書が完成しました。

▶ 次のステップ:
  /cctd:start {StoryID}  — 作業を開始（標準Tasks連携で実行管理）
  /cctd:list              — タスク一覧を確認
  /cctd:web               — Webビジュアライザで進捗を可視化
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/esakat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
