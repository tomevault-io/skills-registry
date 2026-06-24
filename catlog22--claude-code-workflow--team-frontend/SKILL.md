---
name: team-frontend
description: Unified team skill for frontend development. Pure router — all roles read this file. Beat model is coordinator-only in monitor.md. Built-in ui-ux-pro-max design intelligence. Triggers on "team frontend". Use when this capability is needed.
metadata:
  author: catlog22
---

# Team Frontend Development

Unified team skill: frontend development with built-in ui-ux-pro-max design intelligence. Covers requirement analysis, design system generation, frontend implementation, and quality assurance. Built on **team-worker agent architecture** — all worker roles share a single agent definition with role-specific Phase 2-4 loaded from role.md specs.

## Architecture

```
Skill(skill="team-frontend", args="task description")
                    |
         SKILL.md (this file) = Router
                    |
     +--------------+--------------+
     |                             |
  no --role flag              --role <name>
     |                             |
  Coordinator                  Worker
  roles/coordinator/role.md    roles/<name>/role.md
     |
     +-- analyze -> dispatch -> spawn workers -> STOP
                                    |
                    +-------+-------+-------+
                    v       v       v       v
               [analyst] [architect] [developer] [qa]
              (team-worker agents, each loads roles/<role>/role.md)
```

## Role Registry

| Role | Path | Prefix | Inner Loop |
|------|------|--------|------------|
| coordinator | [roles/coordinator/role.md](roles/coordinator/role.md) | — | — |
| analyst | [roles/analyst/role.md](roles/analyst/role.md) | ANALYZE-* | false |
| architect | [roles/architect/role.md](roles/architect/role.md) | ARCH-* | false |
| developer | [roles/developer/role.md](roles/developer/role.md) | DEV-* | true |
| qa | [roles/qa/role.md](roles/qa/role.md) | QA-* | false |

## Role Router

Parse `$ARGUMENTS`:
- Has `--role <name>` → Read `roles/<name>/role.md`, execute Phase 2-4
- No `--role` → `@roles/coordinator/role.md`, execute entry router

## Shared Constants

- **Session prefix**: `FE`
- **Session path**: `.workflow/.team/FE-<slug>-<date>/`
- **CLI tools**: `ccw cli --mode analysis` (read-only), `ccw cli --mode write` (modifications)
- **Message bus**: `mcp__ccw-tools__team_msg(session_id=<session-id>, ...)`

## Worker Spawn Template

Coordinator spawns workers using this template:

```
Agent({
  subagent_type: "team-worker",
  description: "Spawn <role> worker",
  team_name: "frontend",
  name: "<role>",
  run_in_background: true,
  prompt: `## Role Assignment
role: <role>
role_spec: <skill_root>/roles/<role>/role.md
session: <session-folder>
session_id: <session-id>
team_name: frontend
requirement: <task-description>
inner_loop: <true|false>

Read role_spec file (@<skill_root>/roles/<role>/role.md) to load Phase 2-4 domain instructions.
Execute built-in Phase 1 (task discovery) -> role Phase 2-4 -> built-in Phase 5 (report).`
})
```

## User Commands

| Command | Action |
|---------|--------|
| `check` / `status` | View execution status graph |
| `resume` / `continue` | Advance to next step |

## Session Directory

```
.workflow/.team/FE-<slug>-<YYYY-MM-DD>/
├── .msg/
│   ├── messages.jsonl          # Message bus log
│   └── meta.json               # Session state + cross-role state
├── task-analysis.json          # Coordinator analyze output
├── wisdom/                     # Cross-task knowledge
├── analysis/                   # Analyst output
│   ├── design-intelligence.json
│   └── requirements.md
├── architecture/               # Architect output
│   ├── design-tokens.json
│   ├── component-specs/
│   └── project-structure.md
├── qa/                         # QA output
│   └── audit-<NNN>.md
└── build/                      # Developer output
```

## Specs Reference

- [specs/pipelines.md](specs/pipelines.md) — Pipeline definitions and task registry

## Error Handling

| Scenario | Resolution |
|----------|------------|
| Unknown command | Error with available command list |
| Role not found | Error with role registry |
| QA score < 6 over 2 GC rounds | Escalate to user |
| ui-ux-pro-max unavailable | Degrade to LLM general design knowledge |
| Worker no response | Report waiting task, suggest user `resume` |
| Pipeline deadlock | Check blockedBy chain, report blocking point |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/catlog22) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
