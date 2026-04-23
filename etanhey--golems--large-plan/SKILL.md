---
name: large-plan
description: Scaffold and execute folder-based multi-phase implementation plans with async agent collaboration. Creates a docs.local/plans/ folder structure with phases, acceptance criteria, and agent assignments. Each phase produces an independent PR. Supports spawning parallel agents via cmux for concurrent phase execution. Use when planning large features spanning multiple files or packages, multi-PR refactors, coordinating parallel agent work across phases, or breaking down complex specs into executable phases. Triggers on 'large plan', 'multi-phase', 'scaffold plan', 'plan and execute', 'break this down'. NOT for: single-file changes (just do it), simple bugs (use debugging), quick tasks under 30 minutes, or brainstorming (use brainstorming skill first). Use when this capability is needed.
metadata:
  author: etanhey
---

# /large-plan

**Invoke as:** `/large-plan` (single segment).  
**Source:** `~/Gits/golems/skills/golem-powers/large-plan/` (symlinked at `~/.claude/commands/large-plan`).

> Scaffold folder-based plans with phase folders, execute them through the branch-PR-review cycle, and coordinate async agent collaboration.

## Quick Actions

| What you want to do | Workflow |
|---------------------|----------|
| Create a new plan from a description | [workflows/scaffold.md](workflows/scaffold.md) |
| Execute the next phase in a plan | [workflows/execute-phase.md](workflows/execute-phase.md) |
| Start async collab on a phase | [workflows/collab.md](workflows/collab.md) |

---

## Available Scripts

| Script | Purpose | Usage |
|--------|---------|-------|
| `scripts/scaffold-plan.sh` | Create folder-based plan structure | `bash scripts/scaffold-plan.sh <plan-dir> <plan-name> <phase-count>` |

---

## Core Concept

Large plans are folder-based: one folder per phase, each containing a README.md (steps) and findings.md (shared knowledge). A main README.md acts as the index with a progress table and routing.

```
plan-dir/
  README.md              # Index: progress table, routing, execution rules
  collab.md              # Created when parallel phases exist (see below)
  phase-1-name/
    README.md            # Steps for this phase
    findings.md          # Shared knowledge room (agents write here)
  phase-2-name/
    README.md
    findings.md
  ...
```

### Execution Decision: Sequential vs Parallel

**EVERY plan must decide this at scaffold time.** Analyze the dependency graph:

```
Phases with NO cross-dependencies  →  Parallel (collab.md + multiple agents)
Phases that depend on each other   →  Sequential (execute-phase, one at a time)
Mixed                              →  Rounds (parallel within round, sequential between rounds)
```

**Decision tree:**
1. Draw the dependency graph from phase `Depends On` fields
2. Group independent phases into **rounds** (phases in the same round can run in parallel)
3. If ANY round has 2+ phases → create `collab.md` at plan root
4. Add `## Execution Strategy` to the main README.md showing rounds and parallelism

Example:
```markdown
## Execution Strategy

| Round | Phases | Mode | Agents |
|-------|--------|------|--------|
| 1 | Phase 1, Phase 2 | **parallel** (collab) | brainClaude, golemsClaude |
| 2 | Phase 3 (depends on 1+2) | sequential | mainClaude |
| 3 | Phase 4, Phase 5 | **parallel** (collab) | brainClaude, golemsClaude |
```

When a round has parallel phases, the orchestrator:
1. Creates/updates `collab.md` using the [collab protocol](workflows/collab.md)
2. Spawns one agent per phase (Task tool or CLI agents)
3. Each agent's kickoff prompt includes the collab.md path
4. Orchestrator monitors collab.md and advances rounds when all phases are done

### Plan Lifecycle

```
Scaffold plan  →  Analyze dependencies  →  Group into rounds
                                                |
                    ┌───────────────────────────┘
                    ▼
              Round has 1 phase?  →  Execute sequentially (execute-phase)
              Round has 2+ phases? → Create collab.md, spawn agents in parallel
                    |
                    ▼
              All round phases done  →  Advance to next round  →  Repeat
```

### Non-Code Deliverables Check (MANDATORY at scaffold time)

> **Root cause (April 5 overnight sprint):** orcClaude missed the second track — user wanted 9 entity files enhanced for morning walk + code PRs by dawn. Agent only scaffolded the code track.

**At scaffold time, ALWAYS ask:** "Are there non-code deliverables alongside the code phases?"

Common non-code deliverables:
- Data enrichment / content curation (entity files, research docs, grill enhancement)
- Documentation updates (READMEs, portfolio pages, design docs)
- Configuration changes (LaunchAgents, hooks, environment)
- Research outputs (A/B test results, comparative analysis)

If yes, add a separate phase or parallel track for the non-code work. Non-code deliverables are often the user's PRIMARY goal — the code is just infrastructure supporting it.

### Branch Lifecycle (per phase)

```
master -> feature/phase-N-name -> implement -> commit -> push -> PR
  ^                                                            |
  |__ merge <-- approve <-- fix <-- review (CodeRabbit + Cursor Bugbot + DeepSource)
```

### Phase Template

Each phase README follows this template:

```markdown
# Phase N: Name

> [Back to main plan](../README.md)

## Goal
One sentence describing what this phase achieves.

## Time
- **Estimate:** NNmin (basis: [complexity/rolling avg from prior phases])
- **Started:** HH:MM
- **Completed:** —
- **Actual:** —
- **Error ratio:** —

## Round
Round M (parallel with Phase X, Phase Y) OR Round M (sequential).

## Tools
- **Research:** [gemini|cursor|codex] — what to research
- **Code:** [cursor|haiku|sonnet] — what to implement
- **MCPs:** [list relevant MCP servers]

## Steps
1. Step one
2. Step two
3. ...

## Depends On
- Phase X (for Y reason)

## Status
- [ ] Step one
- [ ] Step two
```

### Findings Template

Each phase findings.md is the shared collaboration room:

```markdown
# Phase N Findings

## Decisions
- [timestamp] Decision: ...

## Research
- [timestamp] Agent: Found that ...

## Task Board
| Task | Owner | Status |
|------|-------|--------|
| Research X | gemini | done |
| Implement Y | cursor | in progress |
```

---

## Parallel Execution (Collab Protocol)

When a round has 2+ independent phases, use the **full collab protocol** defined in [workflows/collab.md](workflows/collab.md).

**The orchestrator MUST:**
1. Create `collab.md` at plan root using the template from the collab workflow
2. Fill in all mandatory sections (Goal, Agents, Task Board, Constraints, Gates)
3. Spawn agents with collab path in their kickoff prompt
4. Monitor collab.md and advance rounds when all agents report `done`

**Key rule:** If the human has to tell you to update the collab file, the collab has failed. Agents must self-coordinate.

**Complexity tiers** (from collab workflow):
- **Lightweight** (~40 lines): 2 agents, fully independent work
- **Standard** (~100 lines): 2-3 agents, some dependencies
- **Complex** (~200 lines): 3+ agents, multi-repo, round-based

See [workflows/collab.md](workflows/collab.md) for the full protocol, mandatory sections, update gates, message format, and anti-patterns.

---

## Integration with Other Skills (Building Blocks)

**MANDATORY for every phase:**

| Skill | When | Why |
|-------|------|-----|
| `/pr-loop` | Every phase completion | **The FULL loop** — branch through MERGED. Not optional. |
| `/superpowers:test-driven-development` | All implementation | Red-green-refactor. No code without failing test first. |
| `/superpowers:verification-before-completion` | Before claiming "done" | Evidence before assertions. Always. |
| `/never-fabricate` | Before reporting results | Read() files before summarizing them. |

**Optional per phase:**

| Skill | When to use |
|-------|-------------|
| `/critique-waves` | Verify phase output with parallel agents |
| `/test-plan` | Generate test plans per phase |
| `/prd` | Create PRDs from phase specs |
| `/commit` | CodeRabbit review + atomic commit (step 5 of pr-loop) |
| `/create-pr` | Create PR (step 7 of pr-loop) |

---

## PR Review Cycle (per phase)

After push, automated reviewers comment. Classify each:

| Type | Action |
|------|--------|
| **Real bug** | FIX immediately |
| **Style preference** | Fix if genuinely better |
| **Over-engineering** | SKIP |
| **Out of context** | Comment explaining why |

Repeat push-fix cycle until no real bugs remain.

---

## Platform Features vs Universal Fallbacks

> Claude Code features are listed first. If running on Codex or Cursor, use the universal fallback.
> Full adapter docs: [adapters/](adapters/)

| Feature | Claude Code | Universal Fallback |
|---------|-------------|-------------------|
| **Parallel phase agents** | `Agent(isolation="worktree", run_in_background=true)` | Spawn CLI agents manually: `cd <wt> && codex --full-auto "phase N"` |
| **Phase worktree isolation** | `Agent(isolation="worktree")` — auto-creates + cleans up | `git worktree add -b feature/phase-N ../<dir> master` |
| **Collab file monitoring** | `CronCreate` or `/loop 5m` | `nohup bash -c 'while true; do grep done collab.md; sleep 300; done' &` |
| **Cron cleanup (plan done)** | `CronDelete(<id>)` — mandatory | `kill <bg-monitor-pid>` |
| **Plan mode (spec first)** | `EnterPlanMode → ExitPlanMode` | Write plan to `docs.local/plan/<name>/README.md` manually |
| **Memory persistence** | `brain_store()` / `brain_search()` via BrainLayer | Append to `<plan-dir>/findings.md` |
| **Session resume** | `claude --resume` | Not available — pass `<plan-dir>/README.md` in next session's context |
| **Background phase execution** | `Agent(run_in_background=true)` | `nohup codex --full-auto "..." > phase.log 2>&1 &` |

---

## Time Tracking & Estimation Calibration (MANDATORY)

> Data from April 5 overnight sprint (brainlayer, Codex workers): estimated 90min/phase, actual 15min average. Started at 6x overestimate, auto-calibrated to 1.25x by phase 7. Record timestamps at phase start + PR creation. Without tracking, estimates never calibrate.

### At Scaffold Time

The main README.md progress table MUST include estimate and actual columns:

```markdown
## Progress

| Phase | Status | Estimate | Started | Completed | Actual | Error |
|-------|--------|----------|---------|-----------|--------|-------|
| 1. Setup | ✅ done | 30min | 1:15 AM | 1:28 AM | 13min | 2.3x |
| 2. Search | ✅ done | 30min | 1:30 AM | 1:42 AM | 12min | 2.5x |
| 3. Hybrid | 🔄 active | 15min* | 1:45 AM | — | — | — |
| 4. Evals | ⏳ pending | 15min* | — | — | — | — |

*Auto-recalibrated from rolling avg of phases 1-2 (12.5min → round to 15min)
Rolling calibration: 2.3x → 2.5x → tracking...
```

### At Phase Start (CLOCK IN)

```
brain_store(
  content: "CLOCK IN [plan-name / Phase N]: Started HH:MM. Estimate: NNmin. Basis: [first phase=complexity, later=rolling avg].",
  tags: ["time-tracking", "clock-in", "<project>"],
  importance: 5
)
```

Fill in the phase template's Time section: Started, Estimate.

### At Phase Complete (CLOCK OUT)

```
brain_store(
  content: "CLOCK OUT [plan-name / Phase N]: PR merged HH:MM. Actual: NNmin. Estimated: NNmin. Error: X.Xx. Rolling avg (last 3): NNmin.",
  tags: ["time-tracking", "clock-out", "<project>"],
  importance: 5
)
```

Fill in the phase template's Time section: Completed, Actual, Error ratio.
Update the main README progress table.

### Auto-Recalibration (after 3+ phases)

Once 3 phases have actuals:

```
rolling_avg = average(last 3 actuals)
remaining_phases × rolling_avg = estimated total remaining

Report: "Phases 1-3 done in 38min total. Rolling avg: 12.7min.
         Remaining 4 phases: ~51min at current pace.
         Sprint total ETA: ~89min (original estimate was 630min = 7.1x overestimate)"
```

**Rule:** After 3+ phases, new estimates MUST be within 2x of rolling average. Don't keep estimating 90min when actuals are 15min.

### Why This Matters

User correction (April 5): "No, I'm saying it will take probably hours, not weeks" — after orc estimated a 2-week timeline for work that took one evening. Time tracking turns this from a repeated correction into self-correcting behavior.

---

## Quality Gates (before marking phase done)

| Gate | Check |
|------|-------|
| Typed right | No `any`, proper interfaces |
| Documented | JSDoc on exports, CLAUDE.md updated if needed |
| DRY | No duplicated logic |
| Tests pass | `bun test` / `npm test` green |
| Build passes | No compile errors |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/etanhey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
