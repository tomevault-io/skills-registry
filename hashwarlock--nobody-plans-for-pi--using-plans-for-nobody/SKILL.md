---
name: using-plans-for-nobody
description: Use when starting any conversation — establishes how to find and use skills, requiring skill loading before any response including clarifying questions
metadata:
  author: hashwarlock
---

# Using Plans for Nobody

If there is even a 1% chance a skill might apply to what you are doing, you MUST load and follow the skill.

## How to Access Skills

In pi, use `/skill:name` to load a skill. Skill descriptions are in the system prompt — scan them before every task.

## The Rule

**Load relevant skills BEFORE any response or action.** Even a 1% chance means load the skill.

1. User message received
2. Scan available skills — might any apply?
3. If yes: `/skill:name`, announce "Using [skill] to [purpose]"
4. Follow skill instructions exactly
5. Then respond

## Skill Priority

1. **Process skills first** (nobody-brainstorms, nobody-debugs) — determines HOW to approach
2. **Implementation skills second** (nobody-uses-tdd, nobody-writes-plans) — guides execution

## Red Flags — You're Rationalizing

| Thought | Reality |
|---------|---------|
| "Just a simple question" | Questions are tasks. Check for skills. |
| "Need more context first" | Skill check comes BEFORE clarifying questions. |
| "Let me explore first" | Skills tell you HOW to explore. |
| "Too simple for a skill" | Simple things become complex. Use it. |
| "I remember this skill" | Skills evolve. Read current version. |

## Subagent Workflows

Use the subagent tool for delegated execution:
- `/implement <task>` — scout → planner → worker chain
- `/scout-and-plan <task>` — scout → planner (no implementation)
- `/implement-and-review <task>` — worker → reviewer → worker
- `/review <scope>` — reviewer on recent changes
- `/scout <query>` — fast codebase recon

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
