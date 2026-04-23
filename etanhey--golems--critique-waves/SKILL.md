---
name: critique-waves
description: Use when needing multi-agent verification of complex work. Runs parallel critique agents until consensus. Covers verification, consensus, multi-agent review, validate work. NOT for: simple code reviews (use coderabbit), single-reviewer tasks.
metadata:
  author: etanhey
---

# Critique Waves

> Run iterative waves of verification agents until consensus is achieved. Useful for verifying PRs, code changes, or any work that needs multi-agent validation.

## Quick Actions

| What you want to do | Workflow |
|---------------------|----------|
| Set up verification folder and tracker | [workflows/setup.md](workflows/setup.md) |
| Run a wave of parallel agents | [workflows/run-wave.md](workflows/run-wave.md) |
| Handle failures and iterate to consensus | [workflows/iteration.md](workflows/iteration.md) |

---

## Available Scripts

Execute directly - they handle errors and edge cases:

| Script | Purpose | Usage |
|--------|---------|-------|
| `scripts/init-tracker.sh` | Initialize verification folder with templates | `bash ~/.claude/commands/critique-waves/scripts/init-tracker.sh <branch-name> [goal]` |

---

## Core Concept

Critique Waves uses multiple parallel agents to verify code changes. Each agent independently checks the same files against FORBIDDEN/REQUIRED patterns. By requiring multiple consecutive passes from all agents, we achieve high-confidence verification.

```
                    ┌─────────────────┐
                    │  Setup Phase    │
                    │  - Create folder│
                    │  - instructions │
                    │  - tracker.md   │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
              ┌─────│  Launch Wave    │─────┐
              │     │  (3 agents max) │     │
              │     └────────┬────────┘     │
          ┌───▼───┐    ┌─────▼─────┐   ┌───▼───┐
          │Agent 1│    │  Agent 2  │   │Agent 3│
          └───┬───┘    └─────┬─────┘   └───┬───┘
              │              │             │
              └──────────────┼─────────────┘
                             │
                    ┌────────▼────────┐
                    │ Update Tracker  │
                    │ - Log results   │
                    │ - Check passes  │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
        ┌─────▼─────┐  ┌─────▼─────┐  ┌─────▼─────┐
        │  Any FAIL │  │  All PASS │  │  Goal Met │
        │ Reset=0   │  │ Increment │  │  DONE!    │
        │ Fix issue │  │ passes    │  │           │
        └─────┬─────┘  └─────┬─────┘  └───────────┘
              │              │
              └──────────────┘
                    Loop
```

---

## Decision Tree

**Setting up for a new verification?**
- Create verification folder with templates
- Use: [workflows/setup.md](workflows/setup.md) or `scripts/init-tracker.sh`

**Ready to run agents?**
- Launch wave of 3 parallel agents
- Use: [workflows/run-wave.md](workflows/run-wave.md)

**Agent returned a FAIL?**
- Fix issues, reset pass count, iterate
- Use: [workflows/iteration.md](workflows/iteration.md)

---

## Critical Rules

1. **NEVER run more than 3 agents in parallel** - Hard limit for Mac performance
2. **Agents write to separate files** - No shared state (round-N-agent-X.md)
3. **Update tracker after EACH wave** - Log results immediately
4. **Reset pass count on ANY failure** - Even one fail = reset to 0
5. **All files in ONE folder** - `docs.local/<BRANCH>/`
6. **Maximum 10 rounds** - Escalate to user if no consensus

---

## Example Session

```
Setting up docs.local/feature-branch/...
Created instructions.md and tracker.md

Wave 1: Launching 3 agents...
Results: Agent 1 PASS, Agent 2 FAIL (found forbidden pattern), Agent 3 PASS
Consecutive: 0 (reset due to failure)

Fixing issue found by Agent 2...

Wave 2: Launching 3 agents...
Results: All 3 PASS
Consecutive: 3

Wave 3: Launching 3 agents...
Results: All 3 PASS
Consecutive: 6

...

Wave 7: Launching 3 agents...
Results: All 3 PASS
Consecutive: 21

GOAL ACHIEVED: 21 consecutive passes (exceeded 20 goal)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/etanhey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
