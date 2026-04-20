---
name: agent-orchestration
description: When to use which specialized agent or workflow: planning, code review, security review, refactor, docs. Use for complex features, post-implementation review, or multi-step tasks. Use when this capability is needed.
metadata:
  author: netkenny1
---

# Agent Orchestration

Guidance on when to apply specialized agents and how to run tasks in parallel.

## When to Use This Skill

- User asks for a plan, review, security check, or refactor
- After implementing a non-trivial feature (suggest code review)
- Before committing sensitive or high-risk code (suggest security review)
- When a task clearly benefits from planning or multiple perspectives

---

## Agent Roles (Conceptual)

| Role            | Use for                                      |
|-----------------|----------------------------------------------|
| **Planner**     | Complex features, refactors, multi-step work  |
| **Architect**   | System design, API shape, tech choices        |
| **Code reviewer** | After writing or changing code             |
| **Security reviewer** | Auth, payments, secrets, new APIs     |
| **TDD guide**  | New feature or bug fix (tests first)         |
| **Refactor/cleaner** | Dead code, duplication, clarity         |
| **Doc updater** | Updating docs to match code                  |

---

## When to Invoke (No Explicit Prompt)

- **Complex feature or refactor** → Propose a short plan (or “planner” flow) first
- **Code just written or modified** → Suggest a quick self-review or “code reviewer” pass
- **Bug fix or new feature** → Suggest TDD-style tests where relevant
- **Architecture or API design** → Suggest an “architect” perspective or design doc

---

## Parallel Work

For independent subtasks, do them in parallel instead of strictly one-by-one:

- Example: “Security pass on auth module, performance pass on list API, type-check on shared utils” can be three parallel analyses, then merge findings.

---

## Multi-Perspective Review

For critical or ambiguous work, consider:

- Factual correctness (does it match requirements?)
- Code quality and consistency
- Security and data handling
- Redundancy and dead code

You can run these as separate passes or a single structured review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/netkenny1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
