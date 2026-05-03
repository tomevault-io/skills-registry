---
name: forge
description: >- Use when this capability is needed.
metadata:
  author: vieko
---

# Forge

Delegate complex, multi-step development work to an autonomous agent that builds and verifies code.

## When to Use Forge

- The task targets a different repo (`-C ~/other-project`)
- The work is complex enough to benefit from autonomous agent execution with verification
- You have spec files describing outcomes to implement
- You want to run multiple specs in parallel

## Running Forge Commands

Forge uses the Agent SDK internally. SDK-invoking commands **cannot run inside Claude Code** (nested SDK restriction). The CLI will block with a clear error if you try.

### MCP tools (preferred path)

When MCP tools are available, **always use them** for SDK commands. This is the only reliable execution path from inside Claude Code.

- **`forge_pipeline_start`** — Full pipeline (define -> run -> verify). Use when user says "hand off", "dispatch to forge", "run the full pipeline", or wants end-to-end autonomous execution.
- **`forge_start`** — Individual commands (define, run, audit, proof, verify). Use when the user wants a specific stage, not the full pipeline.
- **`forge_task`** — Poll for task completion. Check every 30-60s after dispatching.
- **`forge_pipeline`** — Read-only pipeline status query.
- **`forge_pipeline_gate`** — Approve or skip pipeline gates.
- **`forge_pipeline_control`** — Pause or cancel a pipeline.

After dispatching via MCP, offer to poll with `forge_task` or run `forge watch` via Bash for live tailing.

### Handoff pattern

When the user says "hand this off to forge", "delegate to forge", or similar:

1. **Summarize your research** into a context string. Include:
   - Key file paths and findings from your exploration
   - Decisions made and rationale
   - Constraints or requirements discovered
   - Keep it concise — the define stage agent will do its own exploration
2. **Call `forge_pipeline_start`** with the goal and packed context
3. **Poll with `forge_task`** for progress, or offer `forge watch`

Example:
```
forge_pipeline_start({
  goal: "implement rate limiting for the API",
  cwd: "/Users/me/project",
  context: "Rate limiting should use token bucket algorithm. Key files: src/middleware/auth.ts, src/routes/api.ts. Redis is already configured in src/lib/redis.ts. Must support per-user and per-endpoint limits. Team decided on 100 req/min default."
})
```

### CLI fallback

If MCP tools are **not available**, present the SDK command for the user to run in a separate terminal:

```
The forge command to run:

  forge run --spec-dir specs/ "implement all"
```

Do NOT run SDK commands via Bash — they are blocked by the nested session guard.

### Non-SDK commands

Run directly via Bash. This includes read-only queries like `status`, `stats`, `watch`, `config`, and `worktree`, plus safe direct mutations like `specs --add`, `specs --resolve`, `specs --manualize`, `specs --archive`, and `worktree mark-review`.

## Operating Model

Treat Forge in two modes:

- Authoring mode: `define`, `proof`, and similar planning/spec-generation flows can operate against the current checkout.
- Execution mode: `run --isolate`, worktree-backed execution, and dependency-level consolidation should be treated as committed-state validation.

Rules that matter in practice:

- spawned isolate worktrees are created from git refs, not from uncommitted filesystem state
- if a worktree-backed run depends on a local Forge/runtime fix, commit that fix first
- after changing Forge runtime code, rebuild with `bun run build` and make sure MCP is using a fresh executor before trusting results
- prefer mechanical verification over narrative agent judgment whenever possible

## Intent Map

Use this section first. It is organized by user intent, not command name.

### Track A New Spec

Use this when a spec file already exists and the user wants Forge to start tracking it as pending.

```bash
forge specs --add specs/new-spec.md
forge specs --add specs/feature/*.md
forge specs --add                          # register all untracked specs
```

Do **not** use `forge audit` just to register a spec.

### Inspect Forge Tasks

Use this when the user wants to see queued/running work across TUI, CLI, or MCP.

```bash
forge tasks
forge tasks --active
forge tasks -C ~/other-repo
```

For MCP:
- use `forge_tasks` to discover repo-scoped task ids
- then call `forge_task` with both `task_id` and `cwd`

Do **not** assume `forge_task` can resolve a task id without repo context unless the same MCP process created it via `forge_start`.

### Check Whether Code Already Satisfies A Spec

Use this when the user wants Forge to triage existing pending specs and auto-resolve already-implemented ones.

```bash
forge specs --check
forge specs --check -C ~/target-repo
```

This is an SDK command. Present it to the user rather than running it inside Claude Code.

### Generate Remediation Specs From Gaps

Use this when the user wants Forge to compare code against specs and write new remediation specs for remaining work.

```bash
forge audit specs/
forge audit specs/auth.md
forge audit specs/ --fix
```

This is different from tracking and different from simple status recovery.

### Manage Spec Lifecycle

Use these direct commands for state management:

```bash
forge specs --resolve spec.md              # mark passed without running
forge specs --unresolve spec.md            # reset to pending and clear run history
forge specs --manualize spec.md            # toggle manual follow-up on/off
forge specs --archive spec.md              # toggle archived state on/off
```

State meanings:
- `manual`: excluded from normal batch execution; use when the remaining work is operator/manual follow-up
- `archived`: excluded from normal batch execution; use when the spec is intentionally no longer active backlog

### Manage Worktree Lifecycle

Use these when the user is operating on Forge worktrees directly rather than through the TUI.

```bash
forge worktree list
forge worktree status <id>
forge worktree mark-review <id>
forge worktree mark-merged <id>
forge worktree prune
forge worktree repair
forge consolidate
forge consolidate --work-group <id>
forge consolidate --all-ready
```

Typical flow:
- `forge worktree mark-review <id>` when implementation is ready for review
- for a single awaiting-review worktree, review and PR the `forge/*` branch directly
- `forge consolidate --work-group <id>` only when a work group has multiple awaiting-review worktrees that need one combined review branch
- `forge worktree mark-merged <id>` after that PR lands
- `forge consolidate --all-ready` for every awaiting-review group

### Inspect Or Debug Forge Runtime State

```bash
forge config
forge executor
forge watch
forge status
forge stats --by-source
```

- `forge config` shows effective config with source tracking
- `forge executor` starts the MCP task executor daemon
- `forge stats --by-source` breaks down runs by CLI vs MCP

### Recover Or Reconcile Forge State

Use this when specs exist on disk but are not tracked, when repo state drifted from Forge state, or when you need to classify historical specs more honestly.

```bash
forge specs --add
forge specs --reconcile
forge specs --manualize spec.md
forge specs --archive spec.md
forge worktree list
forge worktree repair
forge worktree prune --dry-run
forge config
```

Recovery rule of thumb:
- `--add` when the spec exists and should be tracked
- `--reconcile` when Forge has recorded run history to backfill
- `manual` when the remaining work is operator/manual follow-up
- `archived` when the spec is intentionally no longer active backlog
- leave a spec `pending` if you do not have strong evidence to resolve it differently

## Commands

### forge run

```bash
forge run "add auth middleware"                          # Simple task
forge run --spec specs/auth.md "implement this"          # With spec file
forge run --spec-dir ./specs/ "implement all"             # Parallel specs (default)
forge run -C ~/other-repo "fix the login bug"            # Target different repo
forge run --rerun-failed "fix failures"                  # Rerun failed specs
forge run --pending "implement pending"                  # Run only pending specs
forge run --resume <session-id> "continue"               # Resume interrupted session
forge run --plan-only "design API for auth"              # Plan without implementing
forge "quick task"                                       # Shorthand (no 'run')
```

Important flags:
- `-s, --spec <path>` -- Spec file (shorthand resolves via manifest). Prompt becomes additional context.
- `-S, --spec-dir <path>` -- Directory of specs (shorthand resolves via known dirs). Shared-worktree runs execute sequentially by default; use `--isolate` for parallel worktree-per-spec execution. Already-passed specs are skipped.
- `--sequential` -- Run specs sequentially where parallel execution would otherwise be used.
- `-F, --force` -- Re-run all specs including already passed.
- `-B, --branch <name>` -- Run in an isolated git worktree on the named branch. Auto-commits on success, cleans up after.
- `-I, --isolate` -- Create one worktree per spec. Best used after committing required local runtime changes.
- `--in-place` -- Run directly in the current checkout, skip automatic worktree creation. Incompatible with `--branch` and `--isolate`.
- `--concurrency <n>` -- Override auto-detected parallelism (default: freeMem/2GB, capped at CPUs).
- `--sequential-first <n>` -- Run first N specs sequentially, then parallelize.
- `-C, --cwd <path>` -- Target repo directory.
- `-t, --max-turns <n>` -- Max turns per spec (default: 250).
- `-b, --max-budget <usd>` -- Max budget in USD per spec.
- `--plan-only` -- Create tasks without implementing.
- `--dry-run` -- Preview tasks and estimate cost without executing.
- `-v, --verbose` -- Full output detail.
- `-q, --quiet` -- Suppress progress output (for CI).
- `-w, --watch` -- Auto-split tmux pane with live logs.

### forge audit

Reviews codebase against specs. Produces new spec files for remaining work — feed them back into `forge run --spec-dir`.

```bash
forge audit specs/                              # Audit all specs in directory
forge audit specs/auth.md                       # Audit a single spec file
forge audit auth.md                             # Shorthand (resolves via manifest)
forge audit specs/ "focus on auth module"       # With additional context
forge audit specs/ -o ./remediation/            # Custom output dir
forge audit specs/ -C ~/target-repo             # Different repo
forge audit specs/ --watch                      # Auto-split tmux pane with live logs
forge audit specs/ --fix                        # Audit-fix loop (audit -> fix -> re-audit)
forge audit specs/ --fix --fix-rounds 5         # Custom max rounds (default: 3)
```

### forge define

Analyzes codebase and generates outcome spec files from a high-level description. Closes the loop: `forge define` → `forge specs` → `forge <spec-dir> "implement"`.

```bash
forge define "build auth system"                # Generate specs in specs/
forge define "add rate limiting" -o specs/api/  # Custom output dir
forge define "refactor database" -C ~/project   # Different repo
```

### forge review

Reviews recent git changes for bugs and quality issues.

```bash
forge review                                    # Review main...HEAD
forge review HEAD~5...HEAD                      # Specific range
forge review --dry-run -o findings.md           # Report only, write to file
forge review -C ~/other-repo                    # Different repo
```

### forge proof

Generates real test files from implemented specs. Reads specs + codebase, writes `.test.ts` files colocated with source (or in the project's test directory), a `manual.md` human checklist, and a `manifest.json`. Auto-detects test convention and framework. Supports multiple spec paths. `forge prove` is a backward-compatible alias.

```bash
forge proof specs/feature.md                    # Single spec proof
forge proof specs/                              # All specs in directory
forge proof specs/a.md specs/b.md specs/c.md    # Multiple specific specs
forge proof specs/ -o ./custom-proofs/          # Custom manifest output dir
forge proof specs/ -C ~/other-repo              # Different repo
```

### forge verify

Executes generated proof tests and prepares a PR/manifests around the verification run.

```bash
forge verify proofs/
forge verify proofs/ --dry-run
forge verify proofs/ -C ~/other-repo
```

### forge pipeline

Chains define → run → verify into a single automated flow with observable gates. The pipeline process stays alive and polls for gate changes — TUI/MCP approve gates by writing state, not by spawning processes.

```bash
forge pipeline "build auth system"                  # Full pipeline
forge pipeline --from run --spec-dir specs/ "go"    # Start at run with existing specs
forge pipeline --gate-all confirm "careful build"   # Pause at every gate
forge pipeline --resume <pipeline-id>               # Resume paused/failed pipeline
forge pipeline status                               # Show current pipeline state
```

Gates default to: auto (define→run), confirm (run→verify). TUI controls: `a` advance, `s` skip, `p` pause, `c` cancel.

### forge watch

Live-tail session logs with colored output. Auto-follows to next session during batch runs; exits after final session or 60s timeout.

```bash
forge watch                                     # Watch latest session (auto-follows batch)
forge watch <session-id>                        # Watch specific session (no auto-follow)
forge watch -C ~/other-repo                     # Watch in different repo
```

### forge tui

Interactive terminal dashboard for sessions, specs, pipelines, and worktrees.

Specs tab behavior worth knowing:

- specs are grouped by directory, with only the newest directory expanded on load
- `enter` on a spec opens the spec source view
- `r` opens a confirmation dialog for a normal worktree-backed run
- `R` opens a confirmation dialog for an explicit `--in-place` run
- directory selections run through `--spec-dir`, so dependencies and frontmatter are respected
- the command palette exposes `Run In Worktree` and `Run In Place` while the Specs tab is active

If a user asks how to rerun a spec or directory from the TUI, prefer describing the Specs tab actions above instead of redirecting them straight to CLI commands.

Worktrees tab consolidation behavior worth knowing:

- `C` consolidates the selected awaiting-review work group after confirmation
- the command palette exposes `Consolidate Selected Work Group`
- when multiple awaiting-review groups exist, the command palette also exposes `Consolidate All Ready Groups`
- the confirm dialog shows the selected work group, awaiting-review counts, and the generated consolidation branch

### forge status

```bash
forge status                                    # Latest run
forge status --all                              # All runs
forge status -n 5                               # Last 5 runs
forge status -C ~/other-repo                    # Different repo
```

### forge stats

Aggregate run statistics across all results.

```bash
forge stats                                     # Dashboard: runs, cost, success rate
forge stats --by-spec                           # Per-spec breakdown from manifest
forge stats --by-model                          # Per-model breakdown
forge stats --by-source                         # Per-source breakdown (CLI vs MCP)
forge stats --since 2026-03-01                  # Filter runs after date
forge stats -C ~/other-repo                     # Different repo
```

### forge specs

List tracked specs with lifecycle status. When SQLite is present, spec state is stored in the authoritative Forge runtime DB and `.forge/specs.json` is regenerated as a compatibility export. Do not edit `specs.json` manually.

```bash
forge specs                                     # List all tracked specs
forge specs --pending                           # Show only pending
forge specs --failed                            # Show only failed
forge specs --passed                            # Show only passed
forge specs --orphaned                          # Manifest entries with missing files
forge specs --untracked                         # .md files not in manifest
forge specs --add                               # Register all untracked specs
forge specs --add specs/new.md                  # Register specific spec by path/glob
forge specs --resolve game.md                   # Mark spec as passed without running
forge specs --manualize game.md                 # Toggle manual follow-up state
forge specs --archive game.md                   # Toggle archived state
forge specs --unresolve game.md                 # Reset a spec back to pending
forge specs --check                             # Triage pending specs: auto-resolve already-implemented ones via Sonnet agent
forge specs --reconcile                         # Backfill from .forge/results/ history
forge specs --prune                             # Remove orphaned entries from manifest
forge specs --summary                           # Directory-level roll-up (compact view)
```

### forge consolidate

Consolidates awaiting-review worktrees into generated consolidation branches and opens PRs to `main`.

```bash
forge consolidate                               # Most recent awaiting-review work group
forge consolidate --work-group wg-123          # Specific work group
forge consolidate --all-ready                  # Every awaiting-review work group, newest first
forge consolidate --dry-run                    # Preview merge plan
```

Important details:
- default scope is one work group, not every awaiting-review worktree in the repo
- `--all-ready` runs one consolidation per awaiting-review work group
- consolidation branches use human-readable slugs when possible, e.g. `forge/consolidate/gtmeng-689-767-751-738-mql-scoring`
- Forge consolidates into those branches and opens PRs; it does not merge directly into local `main`

### forge worktree

Direct worktree lifecycle management outside the TUI.

```bash
forge worktree list
forge worktree list --status awaiting_review
forge worktree status <id>
forge worktree mark-review <id>
forge worktree mark-merged <id>
forge worktree prune
forge worktree prune --dry-run
forge worktree repair
```

Useful when the user needs to recover, inspect, or clean worktrees without using `forge tui`.

### forge config

Shows the effective Forge configuration for a repo, including source tracking.

```bash
forge config
forge config -C ~/other-repo
```

### forge executor

Starts the MCP task executor daemon. Use this when MCP tasks are queued but nothing is picking them up.

```bash
forge executor
forge serve                                     # alias
```

## Scope And Runtime Notes

Specs may include frontmatter like:

```yaml
---
scope: packages/api
---
```

Use `scope:` when verification should be constrained to a specific package or subtree. It overrides heuristic scope detection.

Forge runtime state may live either:
- in repo-local `.forge/` (legacy/default behavior), or
- under `FORGE_STATE_ROOT`, for example `~/.forge/repos/<repo-key>/...`

Repo-local `.forge/config.json` may remain local even when runtime state is global.

## Important

**Never manually orchestrate parallel forge runs** (e.g. `forge run spec1.md & forge run spec2.md & wait`). Forge handles parallelism, dependency ordering, and skip-passed internally via `--spec-dir`. Manual orchestration bypasses the dependency graph, manifest tracking, and batch grouping.

**Always prefer `--spec-dir`** over running individual specs. It automatically:
- Skips already-passed specs (use `--force` to override)
- Resolves `depends:` frontmatter into a topological execution order
- Tracks all specs in a single batch with grouped cost reporting
- Auto-tunes concurrency based on available memory

**For `--isolate`, commit first.** Isolate worktrees only see committed branch state. If the run depends on local Forge/runtime changes, commit them first, rebuild if needed, and ensure MCP is using a fresh executor.

**`--spec` takes exactly one file.** The prompt is always the last positional argument (a quoted string). Bare file paths without a flag are interpreted as the prompt, not as spec files. To run multiple specs, use `--spec-dir`.

**Shorthand resolution**: spec paths resolve automatically. `forge run --spec login.md` finds the spec via manifest lookup. `forge run --spec-dir gtmeng-580` finds `.bonfire/specs/gtmeng-580/`. Full paths always work too.

**Pipeline is autonomous end-to-end.** `forge pipeline` (or `forge_pipeline_start` via MCP) runs define → run → verify automatically. When you start a pipeline:
- **Wait for it to complete** by polling with `forge_task`. Do NOT commit, push, or create PRs while the pipeline is still running — it owns the repo during execution.
- **Do NOT run individual stages** (e.g. `forge audit`, `forge proof`) after a pipeline — it already ran them.
- **Do NOT commit, push, or create PRs** while a pipeline is running — the verify stage creates a PR automatically when the pipeline completes.
- Only use individual commands when you are NOT using pipeline.

## Common Mistakes

```bash
# WRONG: running SDK commands directly via Bash inside Claude Code
# (blocked by nested session guard — CLAUDECODE=1)
forge run --spec-dir specs/ "implement all"
forge go "build auth"
forge define "add feature"

# WRONG: dispatching to a tmux pane
# (fragile, loses context, MCP is more reliable)
tmux send-keys -t 2.2 'forge run ...' Enter

# RIGHT: use MCP tools from inside Claude Code
forge_pipeline_start({ goal: "build auth", cwd: "/path/to/repo" })
forge_start({ command: "run", description: "implement all", cwd: "/path/to/repo", spec_path: "specs/" })

# WRONG: bare paths without --spec are treated as the prompt string
forge run specs/auth.md specs/login.md

# WRONG: --spec only takes one file, not multiple
forge run --spec specs/auth.md specs/login.md "implement"

# WRONG: manually running specs one-by-one bypasses dependency ordering
forge run --spec specs/01-schema.md "implement" && forge run --spec specs/02-api.md "implement"

# RIGHT: put specs in a directory, use --spec-dir
forge run --spec-dir specs/ "implement all"

# RIGHT: single spec with --spec
forge run --spec specs/auth.md "implement this"
```

## Recipes

### Run all specs in a directory

```bash
forge run --spec-dir gtmeng-580 -C ~/dev/project "implement all"
# Shorthand paths resolve automatically (gtmeng-580 → .bonfire/specs/gtmeng-580/)
# Already-passed specs are skipped; deps on passed specs are treated as satisfied
```

### Run a subset of specs from a directory

If specs use `depends:` frontmatter, `--spec-dir` automatically runs only the ready ones in topological order. Dependent specs wait for their deps to pass — no need to cherry-pick individual specs.

```bash
# Specs declare their dependencies:
#   03-api.md has "depends: [01-schema.md, 02-models.md]"
# Forge resolves the graph — 01 and 02 run in parallel, 03 waits for both
forge run --spec-dir specs/feature/ "implement all"

# Already-passed specs are skipped automatically; their dependents still run
# Use --force to re-run everything including passed specs
forge run --spec-dir specs/feature/ --force "re-verify all"
```

### Spec-driven development

```bash
# 1. Write specs as .md files (see references/writing-specs.md)
# 2. Run them in parallel
forge run --spec-dir ./specs/ "implement all specs"
# 3. Rerun any failures
forge run --rerun-failed "fix failures"
# 4. Check results
forge status
```

### Triage pending specs

```bash
# See what's pending
forge specs --pending
# Auto-resolve specs that are already implemented in the codebase
forge specs --check
# Run whatever is still pending
forge run --pending "implement remaining"
```

### Dependency-aware execution

Specs can declare dependencies via YAML frontmatter. Independent specs run in parallel, dependent specs wait:

```yaml
---
depends: [01-database-schema.md, 02-api-models.md]
---
```

```bash
forge run --spec-dir ./specs/ "implement all"
# Automatically runs in topological order based on depends: declarations
```

### Foundation specs first, then parallelize

When not using `depends:`, number-prefix specs for ordering. Foundations run sequentially before the parallel phase:

```bash
forge run --spec-dir ./specs/ --sequential-first 2 "implement"
# Runs 01-*.md, 02-*.md sequentially, then 03+ in parallel
```

### Audit-then-fix loop

```bash
# Manual: audit then run remediation specs
forge audit specs/ -C ~/project                 # Find gaps
forge run --spec-dir specs/audit/ -C ~/project "fix remaining"

# Automated: convergence loop (audit -> fix -> re-audit, up to 3 rounds)
forge audit specs/ --fix -C ~/project
forge audit specs/ --fix --fix-rounds 5         # More rounds if needed
```

### Resume or fork after interruption

```bash
forge run --resume <session-id> "continue"               # Pick up where you left off
forge run --fork <session-id> "try different approach"    # Branch from that point
```

## Deep-Dive References

| Reference | Load when |
|-----------|-----------|
| [writing-specs.md](references/writing-specs.md) | Writing spec files for forge to execute |
| [parallel-execution.md](references/parallel-execution.md) | Tuning concurrency, understanding cost, monitoring parallel runs |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vieko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
