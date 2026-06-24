---
name: feature-marker
description: > Use when this capability is needed.
metadata:
  author: Viniciuscarvalho
---

# feature-marker

Automates feature development with a 4-phase workflow:

1. **Plan** — Validates/generates PRD, TechSpec, Tasks; creates implementation plan.
2. **Implement** — Executes tasks with progress tracking and per-task checkpoints.
3. **Test** — Runs platform-appropriate test suites and build validation.
4. **PR** — Commits with the enhanced `/commit` command and creates a Pull Request.

## Platform Support

| Stack     | Detection                             | Test              | Lint              |
| --------- | ------------------------------------- | ----------------- | ----------------- |
| iOS/Swift | `*.xcodeproj`, `Package.swift`        | `swift test`      | `swiftlint`       |
| Node.js   | `package.json`                        | `jest` / `vitest` | `{pm} run lint`   |
| Rust      | `Cargo.toml`                          | `cargo test`      | `cargo clippy`    |
| Python    | `pyproject.toml` / `requirements.txt` | `pytest`          | `ruff` / `flake8` |
| Go        | `go.mod`                              | `go test ./...`   | `go vet`          |

## Usage

```
/feature-marker <feature-slug>
```

The skill reads project state on every invocation and presents a single confirmation:

- Checkpoint found → offers to resume from saved phase
- Tasks exist → suggests implement → test → pr
- PRD/TechSpec exist → suggests generating missing files first
- Nothing found → starts from PRD generation

For programmatic use: `/feature-marker --mode <mode> <feature-slug>`
where mode is one of: `full` · `tasks-only` · `spec-driven` · `test-only` · `prd-only`

## Prerequisites

Commands in `~/.claude/commands/`:

- `create-prd.md`
- `generate-spec.md`
- `generate-tasks.md`

Templates in `~/.claude/docs/specs/`:

- `prd-template.md`
- `techspec-template.md`
- `tasks-template.md`

## Project Structure

**Feature documents** (generated in project):

```
./tasks/{feature-slug}/
├── prd.md
├── techspec.md
├── tasks.md
└── {num}_task.md   (individual task files)
```

**State directory** (checkpoint & progress):

```
.claude/feature-state/{feature-slug}/
├── checkpoint.json
├── platform-context.json
├── analysis.md
├── plan.md
├── progress.md
├── test-results.md
└── pr-url.txt
```

## Auto-Installed Dependencies

**product-manager skill** (Plan phase): advanced PRD analysis. Installed via `npx` if missing; non-blocking.

**commit command** (PR phase): conventional commits with pre-commit validation. Copied from bundled `resources/commit.md` if missing; non-blocking.

**spec-workflow skills** (spec-driven mode only): lazy-installed from bundled resources on first use.

## Checkpoint & Resume

If interrupted, re-invoke with the same feature slug:

```
/feature-marker prd-user-authentication
```

The skill detects the checkpoint and offers to resume from the saved phase and task index.

## Configuration

Override defaults with `.feature-marker.json` at the repo root:

```json
{
  "pr_skill": "custom-pr-skill",
  "skip_pr": false,
  "test_command": "npm run test:ci",
  "docs_path": "./tasks",
  "state_path": ".claude/feature-state"
}
```

---
> Source: [Viniciuscarvalho/Feature-marker](https://github.com/Viniciuscarvalho/Feature-marker) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
