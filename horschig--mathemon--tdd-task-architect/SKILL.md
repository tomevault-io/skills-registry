---
name: tdd-task-architect
description: Process for decomposing user stories into TDD-compliant technical tasks. Use during Session 4 (Lead Developer) to ensure architectural units follow the Red-Green-Refactor sequence. Use when this capability is needed.
metadata:
  author: horschig
---

# TDD Task Architect

Ensures implementation plans are testable and follow project-standard TDD cycles.

## Core Workflow

1. **Unit Decomposition**: Break the story into tiny, testable logic units (Services, Models).
2. **Task Sequencing**: Order tasks following the Red-Green-Refactor pattern.
3. **Reference Patterns**: Consult [tdd-patterns.md](references/tdd-patterns.md) for concrete examples and command-line verification steps.

## Principles
- **Small Units**: Tasks must be small enough for one subagent execution.
- **Isolation**: Design services to be testable without the full scene tree.
- **Traceability**: Every task must trace back to the Active User Story.

## Artefacts to Update (when creating tasks)

- Add task entries to `./docs/todo/master_todo.md` under the related story ID and include which files will be changed (e.g., `src/services/MatchService.gd`, `tests/unit/test_match_service.gd`).
- Create unit tests in `./tests/unit/` alongside task implementation and add them to the task checklist.
- Commit test skeletons and task docs before implementing code changes to make the TDD flow explicit.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horschig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
