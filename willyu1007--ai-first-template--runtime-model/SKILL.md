---
name: runtime-model
description: Agent Runtime, Tool Runner, and Hook Runner model. Keywords: runtime, agent, tools. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Runtime Model

This document explains the runtime components and lifecycle semantics used by this AI-first template.

---

## 1. Purpose and Scope

This document is SSOT for:
- What the runtime components are (Agent Runtime / Tool Runner / Hook Runner / Module Runner)
- How a session progresses through lifecycle events
- How ability execution is represented and gated

Out of scope:
- Implementation details of the runtime (later phases)
- Concrete registry generation scripts (later phases)

---

## 2. Runtime Components

| Component | Role | Notes |
|----------|------|-------|
| Agent Runtime | Owns the session lifecycle and AI turn control | Long-lived process |
| Tool Runner | Executes abilities as tasks | Side effects MUST go through abilities |
| Hook Runner | Runs hooks on lifecycle events | Blocking vs non-blocking semantics matter |
| Module Runner | Modular automation exposed as abilities | Not a separate runtime; invoked via Tool Runner |

Key invariants:
- **AI does not run arbitrary side-effecting operations directly.**
- **Ability execution is task-based** (`task.create` 鈫?`task.run`) and hook-guarded.

---

## 3. Execution Flow (Task-Based)

Canonical flow:
1. Read strategy entrypoints (`AGENTS.md`) and activate relevant skills (start with `repo-routing` if unsure)
2. Plan with stages: `understand 鈫?plan 鈫?act 鈫?review`
3. Execute side effects via the Tool Runner:
   - `task.create` (preflight / availability checks)
   - `task.run` (execution with guards)
4. Record outcomes in appropriate workdocs

---

## 4. Lifecycle Events (Hook Timeline)

The runtime emits lifecycle events in a standard sequence:

```
PromptSubmit 鈫?[AI plans] 鈫?PreAbilityCreate 鈫?PreAbilityCall 鈫?PostAbilityCall 鈫?SessionStop
```

Semantics:
- **Blocking, AI-visible** events gate decisions and execution (e.g., preflight/guards).
- **Non-blocking, infra-only** events handle logging/telemetry/build/test/report.

---

## 5. Safety Model (What prevents unsafe behavior)

### 5.1 Tool-owned regions

Some entrypoints contain tool-owned marker blocks. Never edit these blocks manually:
- `<!-- BEGIN ABILITY_INDEX --> ... <!-- END ABILITY_INDEX -->`
- Provider skill wrappers under `/.codex/skills/**` and `/.claude/skills/**` (edit SSOT under `/.system/skills/ssot/**`)

### 5.2 Human approval gates

When a change is security-sensitive, production-affecting, or otherwise high-risk:
- Stop and request explicit human approval.
- Record the checkpoint and decision in workdocs.

---

## 6. Related documents

- `/.system/skills/ssot/repo/architecture-core-mechanisms/repo-architecture-overview/SKILL.md` (high-level architecture map)
- `/.system/skills/ssot/repo/architecture-core-mechanisms/hook-lifecycle/SKILL.md` (workflow playbook; created in Phase 3)
- `/.system/skills/ssot/repo/architecture-core-mechanisms/ability-workflow/SKILL.md` (workflow playbook; created in Phase 3)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
