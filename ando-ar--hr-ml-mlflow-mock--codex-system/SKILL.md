---
name: codex-system
description: | Use when this capability is needed.
metadata:
  author: ando-ar
---

# Codex System - Deep Reasoning Partner

**Codex CLI is your highly capable supporter for deep reasoning tasks.**

> **Detailed rules**: `.claude/rules/codex-delegation.md`

## Context Management (CRITICAL)

**Use subagent to preserve main context.**

| Situation | Method |
|-----------|--------|
| Detailed design consultation | Via subagent (recommended) |
| Debug analysis | Via subagent (recommended) |
| Short question (1-2 sentence answer) | Direct call OK |

## When to Consult (MUST)

| Situation | Trigger Examples |
|-----------|------------------|
| **Design decisions** | "How to design?" "Architecture" |
| **Debugging** | "Why not working?" "Error" "Bug" |
| **Trade-off analysis** | "Which is better?" "Compare" |
| **Complex implementation** | "How to implement?" "How to build?" |
| **Refactoring** | "Refactor" "Simplify" |
| **Code review** | "Review" "Check this" |

## When NOT to Consult

- Simple file edits, typo fixes
- Following explicit user instructions
- git commit, running tests, linting
- Tasks with obvious single solutions

## How to Consult

### Recommended: Subagent Pattern

**Use Task tool with `subagent_type='general-purpose'` to preserve main context.**

```
Task tool parameters:
- subagent_type: "general-purpose"
- run_in_background: true (optional, for parallel work)
- prompt: |
    Consult Codex about: {topic}

    codex exec --model o3 --sandbox read-only --full-auto "
    {question for Codex}
    " 2>/dev/null

    Return CONCISE summary (key recommendation + rationale).
```

### Direct Call (Short Questions Only)

For quick questions expecting 1-2 sentence answers:

```bash
codex exec --model o3 --sandbox read-only --full-auto "Brief question" 2>/dev/null
```

### Workflow (Subagent)

1. **Spawn subagent** with Codex consultation prompt
2. **Continue your work** - Subagent runs in parallel
3. **Receive summary** - Subagent returns concise insights

### Sandbox Modes

| Mode | Use Case |
|------|----------|
| `read-only` | Analysis, review, debugging advice |
| `workspace-write` | Implementation, refactoring, fixes |

## Language Protocol

1. Ask Codex in **English**
2. Receive response in **English**
3. Execute based on advice (or let Codex execute)
4. Report to user in **Japanese**

## Task Templates

### Design Review

```bash
codex exec --model o3 --sandbox read-only --full-auto "
Review this design approach for: {feature}

Context:
{relevant code or architecture}

Evaluate:
1. Is this approach sound?
2. Alternative approaches?
3. Potential issues?
4. Recommendations?
" 2>/dev/null
```

### Debug Analysis

```bash
codex exec --model o3 --sandbox read-only --full-auto "
Debug this issue:

Error: {error message}
Code: {relevant code}
Context: {what was happening}

Analyze root cause and suggest fixes.
" 2>/dev/null
```

## Integration with Research

| Task | Use |
|------|-----|
| Need research first | Claude Explore/WebSearch -> then Codex |
| Design decision | Codex directly |
| Library comparison | Research first -> Codex decision |

## Why Codex?

- **Deep reasoning**: Complex analysis and problem-solving
- **Code expertise**: Implementation strategies and patterns
- **Parallel work**: Background execution keeps you productive

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ando-ar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
