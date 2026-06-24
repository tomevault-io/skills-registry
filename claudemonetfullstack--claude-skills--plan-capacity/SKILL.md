---
name: plan-capacity
description: >- Use when this capability is needed.
metadata:
  author: ClaudeMonetFullStack
---

# Agent Capacity

Assess agent feasibility for a task using Anthropic's proven decision frameworks. Produce a dispatch plan grounded in official best practices, not guesswork.

## Important

- Every classification must trace back to a specific framework from `references/decision-frameworks.md`. No invented heuristics.
- Report what you find. If a task is straightforward, say so. Do not manufacture complexity to justify the skill's existence.
- Read the actual codebase files a task references before assessing. Armchair scoping without reading code is unreliable.
- Take your time with decomposition. A wrong dispatch plan wastes more time than a careful assessment.

## Instructions

### Step 1: Identify the Task

Extract the task from one of these sources:
- `$ARGUMENTS` passed to the skill
- A plan in the current conversation context
- A pasted task description or bug list

Restate the task in one sentence so the user can confirm scope.

### Step 2: Decompose into Atomic Units

Break the task into the smallest units where each unit:
- Has a single, verifiable completion condition (test passes, grep confirms change, file exists)
- Can be described in one sentence
- Touches a bounded set of files

List every unit. Number them.

### Step 3: Run the Enumeration Test

For the task as a whole, answer: **"Can you list all subtasks before starting?"**

- **YES** → This is a workflow problem. Subtasks are predictable. Classify using the six workflow patterns from `references/decision-frameworks.md` (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer).
- **NO** → This needs agent-level autonomy. Identify which specific subtasks are unpredictable and why.

State the answer and reasoning explicitly.

### Step 4: Classify Each Unit

Apply Anthropic's decision ladder to each atomic unit. Consult `references/decision-frameworks.md` for the full framework.

For each unit, assign exactly one classification:

| Classification | Criteria | Claude Code approach |
|---------------|----------|---------------------|
| **Inline** | Single prompt, 1-2 files, clear completion condition | Do it directly in main conversation |
| **Explore subagent** | Read-only investigation, codebase search, file discovery | `subagent_type: Explore` |
| **General subagent** | Self-contained task, clear deliverable, 3-10 tool calls | `subagent_type: general-purpose` |
| **Sequential chain** | Depends on a previous unit's output | Must run after its dependency completes |
| **Human checkpoint** | Ambiguous requirement, judgment call, or moving-target risk | Pause and ask the user |

### Step 5: Check Delegation Specs

For every unit classified as a subagent, verify all four elements of Anthropic's delegation spec are present:

1. **Objective** — Is the goal specific and unambiguous?
2. **Output format** — Will the agent know what to return?
3. **Tool guidance** — Does it know which files/tools to use?
4. **Task boundaries** — Are scope limits explicit?

Flag any unit missing an element. A subagent dispatched without all four will "duplicate work, leave gaps, or fail to find necessary information."

### Step 6: Flag Failure Patterns

Scan the full task for known failure patterns from `references/decision-frameworks.md`:

- **Error accumulation** — Long chains where each step's error compounds
- **Over-delegation** — Units that a single grep or one-file edit would handle faster than a subagent
- **Shared-context splitting** — Phases that need shared context being split across subagents
- **Moving targets** — Fixing unit A changes the landscape for unit B
- **Vague delegation** — Units missing delegation spec elements (caught in Step 5)

### Step 7: Output the Dispatch Plan

```
AGENT CAPACITY ASSESSMENT
═══════════════════════════════════════════
TASK: [one-line summary]
UNITS: [N total]
ENUMERABLE: [YES/NO — from Step 3]

─── DISPATCH PLAN ──────────────────────────

Phase 1 — Parallel [units that can run simultaneously]
  [U1] [INLINE] [one-line description]
  [U2] [SUBAGENT:explore] [one-line description]
  [U3] [SUBAGENT:general] [one-line description]

Phase 2 — Sequential [units depending on Phase 1]
  [U4] [INLINE] [one-line description] (depends on U1)

Phase 3 — Human checkpoint
  [U5] [HUMAN] [what needs user judgment and why]

─── FAILURE RISKS ──────────────────────────
[List any flagged patterns from Step 6, or "None identified"]

─── DELEGATION SPECS ───────────────────────
[For each subagent unit, show the 4-element spec or flag what's missing]

═══════════════════════════════════════════
TIER: [SIMPLE / COMPARISON / COMPLEX — from the three-tier scale]
```

Tier definitions (from Anthropic's multi-agent research system):
- **SIMPLE** — 1 agent, 3-10 tool calls. Most single-file edits and lookups.
- **COMPARISON** — 2-4 parallel subagents, 10-15 calls each. Module-level analysis.
- **COMPLEX** — 10+ subagents with divided responsibilities. Cross-codebase work.

### Step 8: Offer Execution Options

After the report, offer exactly 2 options:
1. "Execute this dispatch plan?" — Proceed to run the phases as described.
2. "Adjust the plan?" — User modifies scope, grouping, or classifications.

## Error Handling

1. **Task is too vague to decompose**: Report: "Task lacks sufficient detail. Specify: [what's missing — files, goals, success criteria]." Do not guess at decomposition.
2. **Task is a single atomic unit**: Report it as SIMPLE tier, INLINE classification. Do not over-decompose. A one-line fix does not need phases.
3. **Task references files that don't exist**: Flag as a potential issue — the task may be creating them, or it may be based on stale information.
4. **No plan in conversation context**: If invoked without `$ARGUMENTS` and no plan exists, say: "No task found. Pass a task description: `/agent-capacity [description]` or create a plan first."
5. **All units are inline**: This is valid. Report SIMPLE tier and note that no subagents are needed. Do not manufacture reasons to use subagents.

## Examples

### Example 1: Simple bug fix

**Input**: "Fix the risk.py per-market cost check — it doesn't include existing position cost"

```
TASK: Fix risk.py per-market cost validation to include existing position cost
UNITS: 1
ENUMERABLE: YES

Phase 1 — Inline
  [U1] [INLINE] Add existing position cost to per-market check in risk.py

FAILURE RISKS: None identified
TIER: SIMPLE
```

### Example 2: Multi-item audit list

**Input**: The 12-item mm-alpha audit list (bugs + dead code + strategy gaps)

```
TASK: Address 12 audit findings across mm-alpha codebase
UNITS: 12
ENUMERABLE: YES

Phase 1 — Parallel (independent fixes)
  [U1] [INLINE] Fix risk.py per-market cost check (1 file, 1 line)
  [U8] [SUBAGENT:general] Remove 4 dead code items (_ensure_sdk_client, get_skew_cents, check_inventory_limits, get_expiry_bonus)
  [U5] [SUBAGENT:explore] Investigate max_quote_size usage — confirm it's truly unused

Phase 2 — Sequential
  [U2] [INLINE] Add pagination to get_fills() (depends on understanding current implementation)
  [U6] [HUMAN] Amend threshold inconsistency — which behavior is correct? (needs user judgment)
  [U7] [INLINE] Add market dequeue logic for out-of-zone markets

Phase 3 — Design required
  [U3] [HUMAN] Adverse-selection detection — needs design approval before implementation
  [U4] [HUMAN] TIME_OF_DAY_FILTERS integration — needs strategy decision

FAILURE RISKS:
- Moving target: if dead code removal (U8) changes imports, U2/U7 may need adjustment
- Over-delegation risk: U1 is a one-line fix, do not subagent it

TIER: COMPARISON
```

---
> Source: [ClaudeMonetFullStack/claude-skills](https://github.com/ClaudeMonetFullStack/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
