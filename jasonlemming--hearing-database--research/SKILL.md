---
name: research
description: Launch focused research into a planning topic, depositing structured findings into learning docs Use when this capability is needed.
metadata:
  author: jasonlemming
---

# Research

You are launching focused research into a topic within the planning system. Your job is to understand what's already known, identify what needs investigation, and launch agents to do the work — depositing structured findings into the topic's learning file.

---

## Arguments

The argument describes what to research. It could be:

- **A topic name** matching a `planning/learning/` file: `analytics`, `api-as-product`, `intelligence-connectivity`, `automation`, `growth-distribution`, `business-viability`
- **A specific question** within a topic: `analytics — what metrics would change product decisions`
- **A broader sweep**: `all investment areas — landscape survey`

---

## Step 1: Understand Current State

Based on the argument, read:

1. **The topic's learning file** (`planning/learning/<topic>.md`) — what's already captured
2. **The quarterly roadmap** (`quarterly-roadmap-q2-2026.md`) — research questions and dependencies for this topic
3. **Any working/reference docs** (`planning/working/`, `planning/reference/`) — has this topic already matured?
4. **Relevant architecture** (CLAUDE.md sections, codebase) — if the topic has technical dimensions

If the argument is a broad sweep, read all relevant learning files.

---

## Step 2: Present Research Brief

Show the user a concise summary:

- **What's already known** — key findings from the learning file, if any
- **What the roadmap asks** — the specific research questions
- **What you'd investigate** — your proposed research directions, prioritized
- **What types of research** — codebase exploration, web research, competitive analysis, technical scoping
- **Scope calibration** — landscape survey (broad, first pass) vs. deep dive (specific, detailed)

If the user provided specific direction in their argument, adapt accordingly. Don't repeat questions they've already scoped — refine them.

---

## Step 3: Launch Agents

After the user confirms direction (or immediately if the direction is clear and specific):

- Launch agents using the **Task tool** with `run_in_background: true` for long-running research
- Each agent gets:
  - Full context on what's already known (from the learning file)
  - Specific questions to investigate
  - The **Deposit Format** below
  - The file path to write findings to
  - Instruction to **append** to the file (below the `## Findings` marker), not overwrite

For multi-topic sweeps, launch **parallel agents** — one per topic.

### Agent Type Selection

- **Codebase questions** (what endpoints exist, what's instrumented, what data is available): Use `subagent_type: "Explore"`
- **Web research** (competitive landscape, market data, best practices): Use `subagent_type: "general-purpose"`
- **Technical scoping** (architecture analysis, integration assessment): Use `subagent_type: "general-purpose"` with codebase access
- **Hybrid** (needs both codebase and web): Use `subagent_type: "general-purpose"`

---

## Step 4: Report

Tell the user:
- What agents were launched and what each is investigating
- Where findings will be deposited (`planning/learning/<topic>.md`)
- How to check on progress (read the file, or check background task output)
- That findings are structured for the user to set terms for a follow-up round when ready

---

## Deposit Format

All research findings must be appended to the topic's learning file in this format:

```markdown
---

## Research: [Brief Label] — YYYY-MM-DD

**Scope:** [What was investigated and at what depth]

**Questions investigated:**
- [Question 1]
- [Question 2]

**Findings:**
- [Finding with specifics — names, numbers, links, code paths, concrete details]
- [Finding]
- [Finding]

**Open questions (for next round):**
- [What remains unclear, needs human input, or requires deeper investigation]

**Suggested next steps:**
- [Specific actions or research directions that would be useful]
```

### Deposit Rules

- **Be concrete.** "Several competitors exist" is useless. "ProPublica Congress API provides free bill/vote data but no hearing transcripts; GovTrack provides XML bulk data" is useful.
- **Include evidence.** Link to code paths, URLs, specific data points. The user needs to evaluate your findings, not trust them blindly.
- **Flag uncertainty.** If you're not confident in a finding, say so. "Likely" and "appears to" are fine — false confidence is not.
- **Separate facts from interpretation.** Present what you found, then what you think it means. The user may interpret differently.
- **Scope your claims.** "Based on the 22 API routers in the codebase" is better than "the API is comprehensive."

---

## Calibrating Depth

- **Landscape survey** (default for first pass): Broad strokes. What exists in the space? What are the obvious options? What's our current state? Useful for the user to orient and set terms for deeper investigation.
- **Focused investigation**: Specific questions with specific answers. "What would it take to add analytics instrumentation to the Flask app?" requires reading the middleware, tracking code, and proposing a concrete approach.
- **Competitive deep dive**: Detailed analysis of specific competitors, alternatives, or reference implementations. Names, features, pricing, technical approaches.

When the user says "minimal involvement" or "first pass," default to **landscape survey**. The goal is useful raw material, not recommendations.

---

## Planning System Context

This skill operates within the three-tier planning system:

| Tier | Folder | What's There |
|------|--------|-------------|
| Learning | `planning/learning/` | Raw capture — where research deposits go |
| Working | `planning/working/` | Iterative drafts — promoted from learning when actively being shaped |
| Reference | `planning/reference/` | Canonical topic docs — graduated from working |

Research always deposits to **learning**. Promotion to working or reference is a separate, user-driven process.

See `planning/README.md` for the full system description.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jasonlemming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
