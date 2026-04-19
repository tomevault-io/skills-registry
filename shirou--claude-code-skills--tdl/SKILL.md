---
name: tdl
description: > Use when this capability is needed.
metadata:
  author: shirou
---

# Traceable Development Lifecycle (TDL)

Structured phases, linked artifacts, verifiable outcomes.

## Workflow Overview

```
Analysis → Requirements → Task (Design + Plan) → Implementation
                 ↑                ↑
                 └── ADR (as needed)
```

Each phase has approval gates. Never advance without explicit approval.
ADRs are created only when an architecture decision needs to be recorded, not as a mandatory phase.

## Document Types and Locations

| Phase | Template | Location | Naming |
| --- | --- | --- | --- |
| Discovery | `analysis.md` | `docs/analysis/` | `AN-<id>-<topic>.md` |
| Requirements | `requirements.md` | `docs/requirements/` | `FR-<id>-<name>.md` / `NFR-<id>-<name>.md` |
| Decisions | `adr.md` / `adr-lite.md` | `docs/adr/` | `ADR-<id>-<title>.md` |
| Task | `task.md` | `docs/tasks/T-<id>-<name>/` | `README.md` |
| Design | `design.md` | `docs/tasks/T-<id>-<name>/` | `design.md` |
| Plan | `plan.md` | `docs/tasks/T-<id>-<name>/` | `plan.md` |

All templates are in `assets/templates/`. For detailed template usage, read [references/templates-guide.md](references/templates-guide.md).

## Setup for New Projects

1. Copy `assets/templates/` to `docs/templates/` in the project
2. Copy `scripts/tdl-new-id.ts` and `scripts/trace-status.ts` to `scripts/`
3. Create directories: `docs/analysis/`, `docs/requirements/`, `docs/adr/`, `docs/tasks/`
4. Scripts require [Bun](https://bun.sh/) runtime

## Creating Documents

### Step 1: Generate ID

Run `./scripts/tdl-new-id.ts` to get a unique 5-character base36 ID. Use this ID in the filename.

### Step 2: Copy Template

Copy the appropriate template from `docs/templates/` and replace all placeholders.

### Step 3: Fill Metadata and Links

Every document must have:
- **Metadata**: Type, Status (use allowed values per template)
- **Links**: Cross-references to related documents. Use `N/A – <reason>` if not applicable.

## Phase-by-Phase Workflow

### 1. Analysis

- **Purpose**: Explore problem space, discover requirements
- **Output**: `docs/analysis/AN-<id>-<topic>.md`
- **Approval gate**: Wait for explicit approval before creating requirements

### 2. Requirements

- **Purpose**: Formalize individual requirements with acceptance criteria
- **Output**: `docs/requirements/FR-<id>-<name>.md` or `NFR-<id>-<name>.md`
- **Key rule**: One requirement per file. Describe problems, not solutions.
- **Approval gate**: Wait for approval before creating task

### 3. Architecture Decision (optional, as needed)

ADRs are not a mandatory step. Create one only when there is an actual architecture decision to record (e.g., choosing between approaches, establishing cross-cutting patterns, making irreversible trade-offs).

- **Purpose**: Document significant design decisions and trade-offs
- **Output**: `docs/adr/ADR-<id>-<title>.md`
- **When to create**: Technology choices, cross-module patterns, security-impacting decisions, irreversible trade-offs
- **When to skip**: Straightforward implementations with no meaningful alternatives
- **Template choice**: Use full ADR for broad-impact decisions (3+ modules, security, platform). Use lite ADR for localized, low-risk decisions.
- **Approval gate**: If created, wait for approval before proceeding

### 4. Task Package

Create `docs/tasks/T-<id>-<name>/` with:
1. `README.md` (from `task.md` template) — always first
2. `design.md` — technical design referencing requirements and ADRs
3. `plan.md` — phased execution plan (only after design is approved)

**Approval gate**: Wait for approval of the task package before implementation.

### 5. Implementation

- Follow `plan.md` phases sequentially
- Each phase is an approval checkpoint: complete it, mark checkboxes `[x]`, then stop and request approval
- Never start the next phase without explicit approval

## Traceability

### Link Labels by Document Type

| Template | Link Labels |
| --- | --- |
| Analysis | `Related Analyses`, `Related Requirements`, `Related ADRs`, `Related Tasks` |
| Requirements | `Prerequisite Requirements`, `Dependent Requirements`, `Related Tasks` |
| ADR | `Impacted Requirements`, `Supersedes ADRs`, `Related Tasks` |
| Task README | `Associated Plan Document`, `Associated Design Document` |
| Design | `Associated Plan Document` |
| Plan | `Associated Design Document` |

### Verification

```bash
bun scripts/trace-status.ts          # Show gaps and consistency
bun scripts/trace-status.ts --check  # CI mode (exit 1 on gaps)
bun scripts/trace-status.ts --write  # Generate docs/traceability.md
bun scripts/trace-status.ts --status # Include coverage metrics
```

Run `--check` before committing documentation changes.

## Key Rules

- **Stage separation**: Complete one stage per approval cycle
- **No skipping**: Design must exist before plan. Plan must exist before implementation.
- **Ambiguity = stop**: Treat unclear instructions as cues to request confirmation
- **Upstream gaps**: If a needed analysis or requirement is missing, create it before proceeding. ADRs are only needed when architecture decisions arise.
- **Status tracking**: Keep document Status metadata current. Mark plan checkboxes immediately on completion.
- **Language**: All documentation in English, dates in YYYY-MM-DD format

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shirou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
