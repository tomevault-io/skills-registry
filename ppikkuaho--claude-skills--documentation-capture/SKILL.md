---
name: documentation-capture
description: Systematic capture of design conversations into project documentation. Scans conversations for undocumented ideas, maps them to the right locations, and dispatches parallel agents to write and verify. Use when this capability is needed.
metadata:
  author: ppikkuaho
---

# Documentation Capture

Systematically extract ideas, decisions, and designs from a conversation and capture them into the right project documents. Ensures nothing falls through the cracks and everything lands in the right place.

**Use when**: After a design conversation, brainstorming session, or any discussion that produced ideas and decisions that need to be preserved in project documentation.

**Don't use when**: Writing new documentation from scratch (just write it), updating a single known file (just edit it), routine commits.

**Important — always load explicitly:** This skill must be invoked via the Skill tool (`/documentation-capture`) every time it is used. Do not freestyle the pattern from memory — always load the skill so you follow the current version precisely.

---

## Core Principles

> **Every idea discussed is either captured or consciously discarded. Nothing falls through the cracks by accident.**

Design conversations produce many ideas at different levels of maturity. The most common failure is not losing ideas entirely — it's capturing them in the wrong place, at the wrong level of detail, or missing the connections to other documents they affect.

> **Favor thoroughness over efficiency. Overshooting is cheap — undershooting loses ideas.**

The cost of additional overhead from being overly thorough is negligible. An extra subagent that finds nothing is a rounding error. A missed idea that should have been captured is real loss. When in doubt, scan wider, check more documents, spawn more reviewers.

---

## Process

### Step 1: Scan — Extract everything discussed

**1a. Identify every discrete item** from the conversation:
- Decisions made
- Design elements (new systems, mechanisms, patterns)
- Principles or rules established
- Requirements identified
- Open questions raised
- Ideas noted for later

Spawn a subagent to do this scan. The scan agent should read the **session transcript file** directly (`.jsonl` in `~/.claude/projects/`), not rely on a summary provided in the prompt. The transcript is the ground truth — every message, every tool call, everything discussed. Point the agent at the transcript file path.

If the transcript is too large for one agent's context, split it into chunks and scan with multiple agents in parallel, then merge results.

The scan produces a numbered list of discrete items, each with a one-line summary. One item per design decision or concept — not per sub-bullet. Group related sub-points under their parent concept.

**1b. Check each item against existing documentation:**

For each item, subagents check: is it already captured? Partially captured? Missing entirely? Captured but now outdated by the conversation?

Produce a status for each item: `new` | `partial` | `outdated` | `already captured`

Filter to only items that need work (`new`, `partial`, `outdated`).

Split the checking across multiple parallel subagents by document area (e.g., one checks architecture docs, one checks Level 4 docs, one checks notes). Don't worry about unbalanced workloads — an agent that finds nothing is not wasted, it's confirmation.

### Step 2: Map — Determine where each item goes

For each item that needs work, identify ALL affected documents — not just the primary one. A design decision might need to be reflected in:
- The primary design document for that system
- Architecture doc (if it changes structural decisions)
- Principles doc (if a new principle emerged)
- Roadmap (if it creates new work items)
- Notes (if it's not mature enough for a standalone doc)
- Related design docs that reference the affected area

**Key rule from Principle 17 (Right-Size Every Cognitive Task):** Each document update is a separate task. Don't ask one agent to update 5 files — that's 5 tasks.

Produce a mapping table:

```
| Item | Status | Primary doc | Also affects |
|------|--------|-------------|--------------|
| Quality gate design | new | QUALITY-GATE.md | ARCHITECTURE.md, ROADMAP.md |
| State model change | outdated | NOTES.md (Agent Awareness) | ARCHITECTURE.md, ROADMAP.md |
```

### Step 3: Check for conflicts

Before writing, verify: does any new content contradict existing documentation? If so, which is correct — the existing doc or the new conversation? Resolve before writing.

This can be combined with Step 2 — the agents checking existing docs in Step 1b are already reading them and can flag contradictions in the same pass.

### Step 4: Execute — Dispatch parallel write agents

One agent per document (or per area if multiple items affect the same document). Each agent receives:
- The specific items it's writing
- The source material (relevant conversation excerpts, or point the agent to the transcript file with specific sections to reference)
- The target document path
- Instructions to read the full document first for context and tone
- Instruction to NOT modify other documents

Agents run in parallel (they write to different files — no conflicts).

**Important:** Give each agent the actual ideas, reasoning, and decisions — not just "write about the quality gate." Include enough source material that the agent can write accurately without having seen the conversation.

### Step 5: Review — Verify what was written

Dispatch review agents (one per area) to compare what was documented against the source requirements. Each reviewer checks:
- Missing items (ideas discussed but not captured)
- Misrepresentations (documented differently from what was discussed)
- Internal contradictions (new content conflicts with existing)
- Unclear language

Review agents run in parallel. They report back with specific issues.

### Step 6: Fix

Address any issues found in Step 5. This may be another round of parallel agents, or direct edits if the issues are small.

---

## Example: What Good Looks Like

From the AI architecture project, after a full design session:

**Step 1a** scanned the session transcript. Produced 108 discrete items across 15 topic areas.

**Step 1b** checked all 108 items across 3 parallel agents (architecture docs, Level 4 docs, notes/project files). Found 106 already captured, 2 needing work.

**Step 2** mapped both items to GUI-DESIGN.md (single document, no cross-references needed).

**Step 3** found no conflicts (additions, not contradictions).

**Step 4** dispatched 1 agent to update GUI-DESIGN.md with dashboard distribution and dual-rendering principle.

**Step 5** dispatched 1 review agent. Found 1 issue: code visualizer missing from L1's dashboard description.

**Step 6** fixed the issue directly (small edit).

**Result:** 108 items verified, 2 gaps found and filled, 1 review issue caught and fixed.

### Earlier in the same project (mid-session capture):

**Step 1** produced 14 items about the quality gate system.

**Step 2** mapped items across 5 documents. Each was a separate task.

**Step 3** found old 4-state model conflicted with new 3-state design — 5 remnant locations.

**Step 4** dispatched 5 parallel write agents, one per document area.

**Step 5** dispatched 5 parallel review agents. Found 14 gaps across all areas.

**Step 6** dispatched 5 parallel fix agents to address all gaps.

---

## Anti-Patterns

- **Summarizing conversation from memory instead of reading the transcript.** The transcript file is the ground truth. Memory drifts, especially after compaction. Always point the scan agent at the actual transcript.
- **Optimizing for fewer subagents.** Don't. An extra agent that finds nothing costs a fraction of what a missed idea costs. Favor thoroughness.
- **One agent updating many files.** Violates right-sizing. Each file is a separate task for a separate agent.
- **Skipping the review step.** The agents that wrote the docs can't objectively evaluate their own work (Principle 4). Separate reviewers catch what writers miss.
- **Capturing ideas at the wrong maturity level.** A half-formed thought goes in NOTES.md, not in a design doc. A fully designed system gets its own Level 4 doc, not a note.
- **Updating primary doc but forgetting cross-references.** Step 2 exists specifically to catch this. Every item maps to ALL affected docs.

---

*Created: 2026-03-17*
*Source: Developed and validated during AI architecture design sessions.*
*Status: V1.1 — updated after first live run. Process proven, improvements from observation incorporated.*

---
> Source: [ppikkuaho/claude-skills](https://github.com/ppikkuaho/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
