---
name: nobody-brainstorms
description: Use when doing any creative work — creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation.
metadata:
  author: hashwarlock
---

# Brainstorming Ideas Into Designs

## Overview

Turn ideas into fully formed designs through collaborative dialogue before writing code.

**Announce at start:** "I'm using the nobody-brainstorms skill to explore this before we build."

## The Process

**Understanding the idea:**
- Check current project state first (files, docs, recent commits)
- Ask questions one at a time to refine the idea
- Prefer multiple choice when possible
- Only one question per message
- Focus on: purpose, constraints, success criteria

**Exploring approaches:**
- Propose 2–3 approaches with trade-offs
- Lead with your recommendation and reasoning
- Present options conversationally

**Presenting the design:**
- Break into sections of 200–300 words
- After each section ask: "Does this look right so far?"
- If they disagree, revise before continuing
- If they agree, continue to next section

**Saving the result:**
- Save approved design to `docs/designs/YYYY-MM-DD-<feature-name>.md`
- Include: goal, approach, key decisions, constraints, success criteria

## After Design Approval

Offer next steps:
1. **Write plan** — use the nobody-writes-plans skill
2. **Implement directly** — use `/implement <feature>`
3. **Set up isolated workspace** — use the nobody-uses-git-worktrees skill

## Red Flags

- Jumping to code before design is approved
- Asking multiple questions at once
- Presenting walls of text without checkpoints
- Skipping project context check

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
