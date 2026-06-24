---
name: ec-os
description: Build and run a low-token multi-engineer or multi-agent collaboration system for software projects. Fallback command: ECOS. Default behavior should be auto-enabled by project AGENTS.md. Use when Codex needs to organize discussion boards, route people into the correct thread, maintain active/completed/help-needed indexes, define status propagation rules, clean up noisy process docs, or turn an ad hoc project workflow into a reusable operating model or skill. Use when this capability is needed.
metadata:
  author: Liam-Dat
---

# Engineering Collaboration OS

## Trigger Aliases

Do not require repeated trigger commands when project AGENTS.md enables default mode.
Keep one fallback correction command only: `ECOS`.

Use this skill to impose structure on chaotic project coordination. Treat the repository as a four-layer system:

1. Organization entry
2. Project control
3. Thread execution
4. Stable knowledge

Read [references/layer-model.md](./references/layer-model.md) when defining file roles.

## Core operating rules

Apply these rules unless the repository already has a stronger convention:

1. Put deep discussion in thread files, not the main board.
2. Make the main board an index, not a transcript dump.
3. Give every task one clear discussion file.
4. Maintain three scan-friendly indexes:
   - active threads
   - completion index
   - help wanted
5. Mark every task file with explicit status fields.
6. Prefer automation over manual index updates.
7. Use GitHub-native templates and CI hooks when repository is on GitHub.

Read [references/status-model.md](./references/status-model.md) when standardizing state propagation and closure.

## Workflow

### 1. Stabilize entry points

Create or normalize these first:

- onboarding SOP
- collaboration board
- discussion workflow
- PM or coordinator guide

If the repo has overlapping guides, reduce them to one canonical path and rewrite the others as short navigation files.

### 2. Define file layers

Map docs into these roles:

- entry docs
- control docs
- thread docs
- stable knowledge docs

If a document mixes roles, split or rewrite it.

### 3. Create routing indexes

Maintain these project-level indexes:

- `ACTIVE_THREADS.md`
- `COMPLETION_INDEX.md`
- `HELP_WANTED.md`

If the repository already has equivalents, standardize them instead of duplicating them.

Execution rule:

- Never maintain these three files manually when scripts are available.
- Generate with:
  - `python3 scripts/generate_indexes.py` (team mode)
  - `python3 scripts/generate_indexes.py --solo` (solo mode)
- Or use unified CLI:
  - `bash scripts/ai-collab.sh generate`
  - `bash scripts/ai-collab.sh generate-solo`

### 4. Standardize task headers

Every active task or proposal file should expose enough state for routing:

- knowledge base status
- task status
- assistance status
- file type
- scope
- canonical output or final file
- next step

If the repo lacks a task header template, create one.

Front matter minimum:

- `skill_id`
- `skill_version`
- `metadata.type`
- `metadata.status`
- `metadata.created_at`
- `metadata.updated_at`

Allowed `metadata.status` values:

- `active`
- `in_progress`
- `pending_review`
- `approved`
- `pending_revision`
- `rejected`
- `completed`
- `archived`

### 5. Enforce state propagation

A task is not truly complete until its state reaches all required layers:

1. task file updated
2. control board updated
3. index updated
4. stable docs updated if system facts changed

If any layer is missing, treat the task as partially closed, not complete.

Validation rule:

- Before merge or handoff, run:
  - `python3 scripts/validate_frontmatter.py`
- If validation fails, task cannot be marked closed.

### 6. Clean process noise

Delete or retire files that are:

- one-off dispatch notes
- obviously replaced
- off-scope proposals
- redundant process guides

Prefer deletion for pure noise. Prefer a short archival header for historically useful files.

## Automation profile (required when tooling exists)

When the repository contains automation scripts, enforce this baseline:

1. Local hooks
- Install hook templates with `bash scripts/ai-collab.sh install`.
- `pre-commit`: metadata validation.
- `post-commit`: index regeneration.

2. CI validation
- Keep a workflow equivalent to `.github/workflows/ai-collab-indexes.yml`.
- CI must validate front matter and regenerate indexes.

3. Issue/PR sync
- Keep a workflow equivalent to `.github/workflows/ai-collab-sync.yml`.
- Use `scripts/sync_issue_event.py` (or equivalent) to create/update thread files from issue/PR events.
- Prefer marketplace auto-commit action over custom shell commit glue when possible.

4. GitHub entry quality
- Provide issue form template (e.g., `.github/ISSUE_TEMPLATE/ai-collaboration-thread.yml`).
- Provide PR template (e.g., `.github/pull_request_template.md`) with status propagation checklist.

## Operating modes

Use mode explicitly to reduce token and coordination overhead:

1. Team mode (default)
- Generate normal help routing.
- `HELP_WANTED.md` includes review and collaboration asks.

2. Solo mode
- Use `--solo` to suppress non-blocking collaboration noise.
- Keep only hard blockers in help routing.

## Decision heuristics

Use these heuristics:

- If a new engineer would need to read more than 3-5 files to know where to act, routing is too weak.
- If a completed task is not visible from the board or completion index, closure is incomplete.
- If help is needed but no file tells others where to jump in, assistance routing is broken.
- If multiple guides explain the same process differently, reduce them to one canonical rule and rewrite the rest as pointers.
- If a repository wants automation, define the state model first and add scripts only after the workflow is stable.
- If automation exists but docs still require manual maintenance, docs are outdated and must be rewritten.

## Outputs

Typical outputs for this skill:

- onboarding SOPs
- operating model docs
- PM/coordinator guides
- active/completed/help-needed indexes
- status header templates
- cleaned thread structures
- closure rules for tasks and issues
- skill-ready procedural docs for reuse across projects
- command-level runbooks (local + CI + GitHub events)

When creating or normalizing concrete files, read [references/file-templates.md](./references/file-templates.md).

## Portability note

The workflow ideas in this skill are portable across products, but the skill package itself should only claim capabilities that are actually present in the folder and supported by the current host.

## References

- File layering and role design: [references/layer-model.md](./references/layer-model.md)
- Status propagation and task closure: [references/status-model.md](./references/status-model.md)
- Standard file templates: [references/file-templates.md](./references/file-templates.md)
- YAML front matter guidance: [references/yaml-frontmatter.md](./references/yaml-frontmatter.md)
- Automation guidance: [references/automation-guide.md](./references/automation-guide.md)

---
> Source: [Liam-Dat/ECOS-AI-skill](https://github.com/Liam-Dat/ECOS-AI-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
