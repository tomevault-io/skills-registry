---
name: ralph-loop-setup
description: Setup guide for Ralph Wiggum autonomous execution loops using Stop hooks. Use when implementing self-referential AI loops for large tasks (20+ files), migrations, or task lists with acceptance criteria. Use when this capability is needed.
metadata:
  author: thegaltinator
---

# Ralph Loop Setup

> "Ralph is a Bash loop." — Geoffrey Huntley (creator)

Ralph Wiggum is a self-referential AI loop technique that feeds Claude's output back into itself until it converges on the correct answer. Named after the Simpsons character, it embodies persistent iteration despite setbacks.

---

## Philosophy (Geoffrey Huntley)

**Core essence:**
```bash
while :; do cat PROMPT.md | claude ; done
```

**Key principles:**
1. **File persistence is the memory** — Claude sees its work in files/git history each iteration
2. **Treat failures as data** — Each iteration refines based on what broke
3. **Define success upfront** — State completion criteria, allow iterative refinement
4. **"Better to fail predictably than succeed unpredictably"**

---

## Architecture

### Two-Agent Structure (Anthropic Research)

| Agent | When | Purpose |
|-------|------|---------|
| **Initializer** | First session only | Sets up environment: init.sh, progress file, feature list, initial commit |
| **Coding** | All subsequent sessions | Makes incremental progress, leaves clean state |

**Critical insight**: Each coding session should leave the codebase in a "clean state" — merge-ready, no bugs, well-documented.

### Three-Phase Workflow (Playbook)

| Phase | Purpose | Output |
|-------|---------|--------|
| **1. Requirements** | Human + LLM identify jobs-to-be-done | `specs/*.md` (one topic per file) |
| **2. Planning** | Gap analysis against existing code | `IMPLEMENTATION_PLAN.md` |
| **3. Building** | One task per iteration, fresh context | Code + tests + commits |

**Why one task per iteration?** Research shows reasoning degrades at ~40% context utilization. Fresh context each cycle keeps Claude in the "smart zone."

---

## Session Startup Routine

Every coding agent session must follow this sequence:

```
1. pwd                          # Orient: where am I?
2. Read git logs + progress     # What happened last session?
3. Read feature list            # What's incomplete?
4. Run init.sh                  # Start dev server
5. Basic E2E test               # Verify app works BEFORE changes
6. Pick ONE incomplete feature  # Work on it
7. Test → Commit → Update progress
```

**Never skip step 5.** If the app is broken, fix it before adding new features.

---

## Backpressure & Convergence

Autonomous loops converge when wrong outputs get rejected by environmental constraints.

### Three Layers of Backpressure

| Layer | Type | Examples |
|-------|------|----------|
| **Downstream Gates** | Deterministic (primary) | Tests, linting, type-checking, build |
| **Upstream Steering** | Discovery | Existing code patterns guide behavior |
| **LLM-as-Judge** | Subjective (last resort) | Tone, UX feel, aesthetics |

**Start with hard gates.** Build tests first. Add LLM-as-judge only for genuinely subjective criteria.

### JSON > Markdown for Tracking

Use JSON for feature/task tracking, not Markdown. Claude is less likely to inappropriately modify JSON files.

```json
{
  "category": "functional",
  "description": "User can send a message and receive AI response",
  "steps": ["Open chat", "Type message", "Press enter", "Verify response"],
  "passes": false
}
```

---

## File Structure

```
project/
├── ralph/
│   ├── PROMPT_init.md          # Initializer agent prompt
│   ├── PROMPT_build.md         # Coding agent prompt
│   └── specs/                  # Requirements (one topic per file)
│       ├── auth.md
│       └── chat.md
├── init.sh                     # Dev server startup script
├── claude-progress.txt         # Session log (append-only)
├── feature_list.json           # Feature tracking (passes: true/false)
├── IMPLEMENTATION_PLAN.md      # Task list from planning phase
└── .claude/
    ├── settings.json           # Hook configuration
    └── ralph-loop.local.md     # State file (auto-managed)
```

---

## Quick Start

### 1. Create State File

```bash
cat > .claude/ralph-loop.local.md <<'EOF'
---
active: true
iteration: 1
max_iterations: 100
completion_promise: "ALL_TASKS_COMPLETE"
started_at: "2024-01-15T00:00:00Z"
---

[Your prompt content here - re-fed each iteration]
EOF
```

### 2. Configure Stop Hook

In `.claude/settings.json`:
```json
{
  "hooks": {
    "Stop": [{"hooks": [{"type": "command", "command": ".claude/stop-hook.sh"}]}]
  }
}
```

### 3. Start Loop

```bash
claude --dangerously-skip-permissions
```

The Stop hook intercepts exit, increments iteration, re-feeds the prompt.

---

## Failure Modes & Fixes

| Problem | Cause | Solution |
|---------|-------|----------|
| **Declares victory early** | No feature tracking | Create `feature_list.json` with `passes: false` for all features |
| **Leaves bugs/mess** | No cleanup routine | Require git commit + progress update at end of each session |
| **Marks done prematurely** | No verification | Mandatory E2E test before marking `passes: true` |
| **Wastes time orienting** | No init script | Create `init.sh` with dev server startup |
| **Same error repeating** | Stuck in loop | Add escape: "If same error 3x, document blocker, skip to next task" |
| **Context overwhelm** | Too much state | Reference files by path, single source of truth for status |

---

## Completion Promise

The completion promise is how Ralph knows to stop.

**Good promises:**
- `ALL_TASKS_COMPLETE` — Clear, unambiguous
- `TESTS_PASSING` — Verifiable condition
- `MIGRATION_DONE` — Concrete end state

**Bad promises:**
- `DONE` — Too vague, Claude might lie
- `READY` — Subjective

**Critical rule in your prompt:**
```
ONLY output <promise>YOUR_PROMISE</promise> when the statement is
genuinely and verifiably TRUE. Do NOT lie to escape the loop.
Check your work. Run the tests. Verify completion.
```

---

## Iteration Limits

Always set `max_iterations` as a safety net:

| Task Size | Recommended Max |
|-----------|-----------------|
| Small (5-10 tasks) | 20-30 |
| Medium (10-30 tasks) | 50-75 |
| Large (30-60 tasks) | 100-150 |

Formula: `max_iterations = num_tasks * 2 + buffer`

---

## When to Use Ralph

### Good Use Cases
- Large implementation tasks (20+ files)
- Task lists with clear acceptance criteria
- Refactoring projects
- Test coverage expansion
- Migration tasks

### Bad Use Cases
- Research/exploration (needs concrete deliverables)
- Ambiguous requirements (will loop forever)
- Tasks requiring human judgment (design, UX)
- One-off quick fixes (overkill)

---

## Git Worktrees (Recommended)

Always use a separate git worktree for Ralph loops:

```bash
git worktree add ../MyProject-Ralph feature-branch
cd ../MyProject-Ralph
# ... setup Ralph files ...
```

**Why:**
- Isolates changes from main work
- Easy to review before merging
- Can abandon if it goes wrong

---

## Topic Scope Test

When writing specs, each file should cover ONE topic.

**Test:** Can you describe it in one sentence without "and"?

- "Help users send messages" (one topic)
- "Send messages and manage contacts and handle notifications" (multiple topics - split it)

---

See **reference.md** for full code examples including stop-hook.sh, prompt templates, and troubleshooting.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thegaltinator) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
