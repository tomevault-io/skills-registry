---
name: socratic-clarify
description: Convert vague or underspecified user requests into an execution-ready brief through Socratic clarification. Use when scope, definitions, constraints, tradeoffs, success criteria, assumptions, risks, or decision thresholds are unclear, especially before planning/execution, in multi-step work, multi-agent workflows, or higher-stakes tasks where misalignment is costly. Use when this capability is needed.
metadata:
  author: swepalm
---

# Socratic Clarification Gate

Transform an ambiguous request into a brief another agent can execute with minimal reinterpretation.

## Core Behavior

- Restate the objective in one concrete sentence.
- Ask only high-leverage questions that would change the solution.
- Infer answers from provided context when defensible; never invent facts.
- Mark unresolved items as pending.
- Decide whether to proceed or block execution.

## Output Contract

Return output in exactly this order:

1. **Clarified objective**
2. **Key constraints and success criteria**
3. **Assumptions (explicit)**
4. **Socratic questions (answered if possible, otherwise pending)**
5. **Risks and failure modes**
6. **Recommended next step**

Allowed values for section 6:
- `Proceed to planning with this brief`
- `Block execution until pending questions are answered`

Write concise, concrete, testable statements. Prefer measurable criteria over subjective wording.

## Procedure

### Step 0: Select mode

Choose one or more modes:

- **Mode A: Clarity**
  Goal: force precise definitions and scope boundaries.
- **Mode B: Truth**
  Goal: stress-test assumptions and verification needs.
- **Mode C: Design**
  Goal: surface priorities, tradeoffs, and acceptable failure.

Default to A + C. Add B when correctness, freshness, or evidence quality materially affects outcomes.

### Step 1: Draft objective

Write one sentence that states deliverable, audience, and intended result.

If one sentence cannot be written precisely, treat that as proof clarification is still required.

### Step 2: Ask high-leverage questions

Ask 5 to 8 questions unless the request is trivially clear.

Ask only questions whose answers materially change solution shape, quality bar, or risk profile.

Prioritize questions that force:
- definitions
- scope boundaries
- concrete examples
- constraints and prohibitions
- success criteria
- tradeoffs
- risk tolerance

Avoid generic filler questions.

Use `references/question-bank.md` to select targeted question prompts.

### Step 3: Resolve what can be resolved

For each question:
- mark as `answered (inferred from user context)` when evidence exists
- mark as `pending` when evidence is missing

Record any assumption required to move forward under **Assumptions (explicit)**.

### Step 4: Build execution-ready brief

Translate clarified intent into:
- hard constraints
- explicit non-goals
- testable acceptance criteria
- known risks and detection signals

Make success criteria checkable (for example: required sections, output length range, inclusion/exclusion rules, evaluation rubric).

### Step 5: Gate decision

Return `Block execution until pending questions are answered` if any condition is true:

- more than 2 pending questions affect core scope
- definition of success is missing
- key constraints are unknown and risk is non-trivial
- plausible harm/policy/irreversibility concerns remain unresolved

Otherwise return `Proceed to planning with this brief`.

## Quality Check

Score the brief from 1 to 5 for each axis before finalizing:

- **Specificity**: Is the objective unambiguous?
- **Testability**: Are success criteria checkable?
- **Constraint coverage**: Are key constraints captured?
- **Risk awareness**: Are plausible failure modes named?
- **Assumption hygiene**: Are assumptions explicit and minimal?

If any axis is 2 or below, ask the minimum additional questions required and block execution.

## Multi-Agent Hand-off

When used in orchestrated workflows:

1. Run this skill before spawning workers.
2. Pass the resulting brief unchanged as the shared task contract.
3. Re-run this skill whenever new constraints or goals appear.
4. Prevent independent worker reinterpretation of scope.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/swepalm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
