---
name: tdd-pair-loop
description: TDD pair programming loop methodology. Defines the red-green-refactor cycle, agent coordination protocol, HANDOFF schema, and state management for the Driver (test writer), Navigator (code writer), and Researcher (read-only context) agents. Use when this capability is needed.
metadata:
  author: pertrai1
---

# TDD Pair Programming Loop

This skill defines the methodology for running a strict TDD pair programming session with three coordinating agents: Driver, Navigator, and Researcher.

## Session Modes

This skill supports two modes of operation:

- **Auto-Solve Mode** (default): Agents solve the problem autonomously. Best for generating reference solutions.
- **Coach Mode** (`--mode=coach`): The learner writes all implementation code with agent guidance and follows a progressive test-ownership ladder. Best for learning patterns and building problem-solving skills.

## Core Principle

**Red → Green → Refactor**, one test at a time, with strict role boundaries.

### Auto-Solve Mode

- The Driver writes ONE failing test (RED)
- The Navigator writes MINIMAL code to pass it (GREEN)
- The Navigator optionally refactors (REFACTOR, tests stay GREEN)
- Repeat until the problem is fully solved

### Coach Mode

- The Driver writes ONE failing test (RED)
- The learner progressively takes on test responsibility (L1 propose -> L2 sketch -> L3 author)
- The Coach inserts TODO comments describing what must become true (GUIDE)
- The learner states the pattern and invariant (CHECKPOINT)
- The learner writes the implementation code (CODE)
- The Coach reviews, runs tests, and advances or gives feedback (REVIEW)
- Repeat until the problem is fully solved

## Agent Roles and Boundaries

### Auto-Solve Mode Agents

| Agent      | Writes                    | Cannot Touch         | Runs Tests               |
| ---------- | ------------------------- | -------------------- | ------------------------ |
| Driver     | `*.test.ts` only          | Implementation files | Yes — must confirm RED   |
| Navigator  | Implementation files only | Test files           | Yes — must confirm GREEN |
| Researcher | Nothing                   | All files            | No                       |

### Coach Mode Agents

- **Driver** — Writes: `*.test.ts` by default, reviews L3 learner tests; Cannot touch: implementation files; Runs tests: yes (must confirm RED)
- **Coach** — Writes: TODO comments only; Cannot touch: test files and functional code; Runs tests: yes (after learner edits)
- **Learner** — Writes: implementation code and one new test on L3 cycles; Cannot touch: existing tests outside the current cycle or unrelated files; Runs tests: no (Coach runs them)
- **Researcher** — Writes: nothing; Cannot touch: all files; Runs tests: no

These boundaries are absolute. No exceptions.

## The TDD Cycle (Step by Step)

The phases below apply to both modes unless marked otherwise. Phase 2 differs between modes.

### Phase 1: SETUP (Orchestrator Only)

1. Select or receive a LeetCode problem
2. Determine difficulty level and create directory: `leetcode/{difficulty}/{number}-{name}/`
3. Create `README.md` with problem statement from LeetCode using **pure markdown** (no HTML elements — see Markdown Standards below)
4. Create empty implementation file: `{name}.ts` with a stub export
5. Create empty test file: `{name}.test.ts` with describe block and import
6. Create a git branch: `tdd/{number}-{name}`

#### Stub Implementation File

```typescript
// {name}.ts
export function functionName() {
  // TODO: implement via TDD
  throw new Error('Not implemented');
}
```

#### Stub Test File

```typescript
// {name}.test.ts
import { describe, it, expect } from 'vitest';
import { functionName } from './{name}';

describe('{Problem Name}', () => {
  // Tests will be added one at a time by the Driver
});
```

### Phase 2: TDD LOOP (Repeat)

#### Auto-Solve Mode

##### Step A — Driver (RED)

1. Driver reads the problem statement and any previous HANDOFF context
2. Driver writes exactly ONE new `it()` block in the test file
3. Driver runs: `npx vitest run {test-file} --reporter=verbose`
4. Driver confirms the new test FAILS (RED)
5. Driver outputs a HANDOFF block

##### Step B — Researcher (OPTIONAL)

Only triggered if the Driver or Navigator includes `RESEARCH:` in their HANDOFF.

1. Researcher reads the question
2. Researcher looks up: techniques, patterns, existing repo examples, docs
3. Researcher returns concise findings
4. Orchestrator passes findings to the next agent (Navigator or Driver)

##### Step C — Navigator (GREEN + optional REFACTOR)

1. Navigator reads the Driver's HANDOFF (and Researcher findings if any)
2. Navigator writes the MINIMAL code change to make the failing test pass
3. Navigator runs: `npx vitest run {test-file} --reporter=verbose`
4. Navigator confirms ALL tests PASS (GREEN)
5. (Optional) Navigator refactors, then runs tests again to confirm still GREEN
6. Navigator outputs a HANDOFF block

##### Step D — Loop Decision

- If more behaviors need testing → go to Step A (Driver writes next test)
- If all acceptance criteria are met → proceed to Phase 3

---

#### Coach Mode

##### Test-Ownership Ladder (default)

- **Level 1 — Test intent (cycles 1-2):** Learner proposes case + expected output; Driver writes the test.
- **Level 2 — Assertion sketch (cycles 3-4):** Learner specifies input/output + assertion shape; Driver finalizes syntax.
- **Level 3 — Learner-authored test (cycle 5+):** Learner writes the full `it()` block; Driver reviews and runs RED.
- **De-escalation rule:** If the learner is stuck or severely time-boxed, step down one level for one cycle, then resume progression.

##### Step A — Driver (RED)

Run RED with the active test-ownership level:

1. Driver reads the problem statement and any previous HANDOFF context
2. Driver applies the active ownership level:
   - L1/L2: Driver writes exactly ONE new `it()` block using learner-provided test intent/sketch
   - L3: Learner writes exactly ONE new `it()` block; Driver reviews and keeps/adjusts it only for test clarity/syntax
3. Driver runs: `npx vitest run {test-file} --reporter=verbose`
4. Driver confirms the new test FAILS (RED)
5. Driver outputs a HANDOFF block

##### Step B — Researcher (OPTIONAL)

Same as auto-solve mode. Only triggered if `RESEARCH:` appears in a HANDOFF.

##### Step C — Coach (GUIDE)

1. Coach reads the Driver's HANDOFF (and Researcher findings if any)
2. Coach inserts **1-3 TODO comments** in the implementation file
   - Each TODO describes one atomic behavior change and one invariant
   - TODOs say **what must become true**, not how to type it
   - Granularity: each TODO corresponds to roughly 5-15 lines of code
3. Coach asks the **pattern checkpoint question**:
   > "What pattern are you using, what are the key state variables, and what invariant must hold?"
4. Coach waits for the learner's response
5. Coach evaluates the learner's pattern statement:
   - **Correct** → "Good. Go ahead and implement the TODOs."
   - **Partially correct** → Asks clarifying questions (does NOT give the answer)
   - **Wrong** → Guides with Level 1 questions until the learner reaches the right pattern
6. Coach records the test-ownership level and learner test contribution for the cycle
7. Coach outputs a HANDOFF block

##### Step D — Learner Codes

The learner writes the implementation code (and on L3 cycles has already authored the new test in Step A), then responds with one of:

- **"continue"** → Advance to Step E (Review)
- **"feedback"** → Advance to Step F (Evaluation)

##### Step E — Coach Review (after "continue")

1. Coach reads the learner's code changes
2. Coach runs: `npx vitest run {test-file} --reporter=verbose`
3. **If ALL tests pass:**
   - Remove completed TODO comments from the implementation file
   - Acknowledge what was done well (1 sentence max)
   - Optionally ask a brief reflection question about the invariant or complexity
   - Commit progress: `git add . && git commit -m "learn: cycle {N} - {description}"`
   - Proceed to next cycle (back to Step A)
4. **If tests fail:**
   - Show the failure output
   - Identify which TODO's invariant is violated (without giving the fix)
   - Ask a Level 1 guiding question about the failure
   - Do NOT insert new TODOs — the learner must fix the current ones first
   - Stay in the current cycle (back to Step D)
5. Coach outputs a HANDOFF block

##### Step F — Coach Feedback (after "feedback")

1. **Coach reads the learner's code changes first** — the learner has written code and is requesting feedback ON that code. Coach MUST read and understand the diff before evaluating.
2. **Coach runs tests**: `npx vitest run {test-file} --reporter=verbose` — notes which pass/fail and includes results in feedback.
3. Coach pauses progression (no new TODOs)
4. Coach scores the learner on 5 categories (1-5 scale), referencing the specific code the learner wrote:
   - **Problem Solving**: Pattern identification, hint levels needed, complexity awareness
   - **Coding**: Translation of intent to code, cleanliness, idioms
   - **Verification**: Edge case consideration, invariant testing, self-debugging
   - **Communication**: Pattern/invariant articulation, explanation clarity
   - **Complexity Analysis**: Time/space reasoning accuracy
5. Each category gets 1-2 concrete observations quoting specific lines or variable names from the learner's code
6. Coach acknowledges what the learner got right before critiquing
7. Coach identifies one prioritized improvement target
8. Coach says: "Say 'continue' when you're ready to resume."
9. Coach outputs a HANDOFF block
10. When the learner says "continue", return to Step D (same cycle, same TODOs)

##### Step G — Loop Decision

- If more behaviors need testing → go to Step A (next test is created per active ownership level)
- If all acceptance criteria are met → proceed to Phase 3

##### Coach Mode 3-Level Hint Ladder

When the learner is stuck, the Coach escalates hints progressively:

| Level | Name                  | What Coach Provides                                         | When to Use                                    |
| ----- | --------------------- | ----------------------------------------------------------- | ---------------------------------------------- |
| 1     | Guiding Questions     | Questions that lead toward the answer without naming it     | Always start here                              |
| 2     | Pattern + Outline     | Name the pattern, outline the steps (no code)               | After 1-2 failed attempts or conceptual gap    |
| 3     | Micro-Step Pseudocode | Pseudocode for just the next micro-step (not full solution) | After 3+ failures or explicit "reveal" request |

**Stuck safeguards:**

- Same assertion fails 3 times → auto-escalate hint level
- Learner's invariant repeatedly wrong → pause for mini-lesson on the pattern
- Syntax struggle (not algorithm) → provide syntax snippet directly
- Learner says "I'm stuck" → ask which TODO, then Level 2 hint
- Learner says "reveal" → Level 3 for current TODO only

### Phase 3: POST-MORTEM

The post-mortem format differs by mode.

#### Auto-Solve Mode

1. Copy `POST_MORTEM_TEMPLATE.md` to the problem directory as `POST_MORTEM.md`
2. An expert reviewer agent fills out the post-mortem using the `review-solution` skill:
   - Problem description in own words
   - Solution exploration: approaches considered, final analysis
   - Time and space complexity with explanation
   - Pattern recognition and key insight
   - Related problems (2-3 similar ones)
   - Edge cases handled and missed
   - Retrospective and key takeaways
   - Self-rating rubric

#### Coach Mode (Interview Debrief)

In coach mode, the post-mortem is written as a **senior software engineer who just concluded a coding interview with the learner**. Do NOT use `POST_MORTEM_TEMPLATE.md`. Write from the interviewer's perspective (for example, "I observed...", "I would move forward..."), never as learner self-reflection. Instead, create `POST_MORTEM.md` with:

1. **Session Overview** — Summary of how the session went, overall impression
2. **5-Category Scoring** (1-5 scale each) — Problem Solving, Coding, Verification, Communication, Complexity Analysis. Each category must include Strengths, Areas for Improvement, and evidence from the session.
3. **Pattern Recognition** — Pattern used, key insight, checkpoint results, 2-3 related problems to practice
4. **Session Progression** — Table of cycles with test name, test-ownership level (L1/L2/L3), hint level used, checkpoint result, and notes
5. **No-Hire Trigger Check** — list critical triggers (if any), recovery evidence, and guardrail applied
6. **Overall Assessment** — Interview recommendation (Recommend / Lean Recommend / Neutral / Lean No / Do Not Recommend), explicit hiring decision (Strong Hire / Hire / Lean Hire / Lean No Hire / No Hire), move-forward call (Yes/No), confidence (High/Medium/Low), summary, strengths to build on, priority areas for growth, and recommended next steps (specific problems and techniques)

The tone must be professional, specific, and constructive — like a real interview debrief. Every observation must reference something that happened in the session (actual code, actual checkpoint responses, actual hint escalations). Do NOT write this as the learner ("I learned", "I struggled"). Apply no-hire guardrails: if 2+ critical triggers occur without clear recovery, cap at Lean No Hire; if any critical trigger is unresolved at session end, outcome must be No Hire with no move-forward. Critical triggers include repeated inability to state invariants, repeated Level 3 dependency on core logic, inability to explain final complexity/correctness, repeated no-strategy debugging loops, and unresolved critical correctness bugs. See `.claude/commands/tdd-pair.md` Phase 3 for the full template.

#### Quality Checks (Both Modes)

1. Run quality checks:
   - `npx eslint {impl-file}` — fix any issues
   - `npx prettier --write {impl-file} {test-file}` — format code
   - `npx markdownlint leetcode/{difficulty}/{number}-{name}/README.md` — fix any violations
   - `npx markdownlint leetcode/{difficulty}/{number}-{name}/POST_MORTEM.md` — fix any violations
   - `npx vitest run {test-file}` — final confirmation all tests pass

### Phase 4: UPDATE ROOT README

Update the root `README.md`:

1. Increment the LeetCode "Problems Solved" count in the Overview table
2. Increment the difficulty-specific count in the appropriate `<summary>` tag (e.g., "Easy Problems (N solved)")
3. Add the problem link in the correct difficulty section and category subsection
4. If the problem matches a pattern in "Problems by Pattern", add it there too
5. Run `npx markdownlint README.md` — fix any violations

### Phase 5: PULL REQUEST

1. Stage all changed files: `git add leetcode/{difficulty}/{number}-{name}/ README.md`
2. Commit with message: `feat: solve {Problem Name} (#{number}) via TDD`
3. Push branch: `git push -u origin tdd/{number}-{name}`
4. Create PR:

   ```bash
   gh pr create \
     --title "feat: solve {Problem Name} (#{number})" \
     --body "## Problem\n{link}\n\n## Approach\n{summary from post-mortem}\n\n## Complexity\n- Time: O(...)\n- Space: O(...)\n\n## TDD Cycles\n{number of red-green cycles completed}" \
     --label "code challenge"
   ```

5. PR is assigned to the user for review

## HANDOFF Schema

Every Driver, Navigator, and Coach output MUST include a HANDOFF block. This is the contract that makes agent coordination work.

### Driver HANDOFF (RED phase)

```markdown
## HANDOFF

- **Phase**: RED
- **Cycle**: {number}
- **Test file**: {path}
- **New test**: {test name}
- **What behavior is specified**: {one sentence}
- **Test output**: {vitest FAIL output}
- **Test ownership level**: {L1 | L2 | L3 | N/A}
- **Learner test contribution**: {none | proposed case/output | assertion sketch | authored full `it()` block}
- **Next step**: {what implementation owner should do next | DONE}
- **Research needed**: {question | none}
```

### Navigator HANDOFF (GREEN/REFACTOR phase — auto-solve mode)

```markdown
## HANDOFF

- **Phase**: GREEN | REFACTOR
- **Cycle**: {number}
- **Implementation file**: {path}
- **What was implemented**: {one sentence}
- **Approach**: {algorithm/technique}
- **Test output**: {vitest PASS output}
- **All tests passing**: YES | NO
- **Refactored**: YES | NO — {what changed}
- **Next step**: {ready for next test | DONE}
- **Research needed**: {question | none}
```

### Coach HANDOFF (COACH phase — coach mode)

```markdown
## HANDOFF

- **Phase**: COACH
- **Cycle**: {number}
- **Mode**: continue | feedback
- **Implementation file**: {path}
- **TODOs inserted**: {list of TODO descriptions added this cycle, or "none — fixing previous"}
- **TODOs completed**: {list of TODOs the learner successfully implemented, or "none"}
- **Test ownership level**: {L1 | L2 | L3}
- **Learner test contribution**: {none | proposed case/output | assertion sketch | authored full `it()` block}
- **Hint level used**: {0 (no hints needed) | 1 | 2 | 3}
- **Pattern checkpoint**: {PASS — learner stated pattern correctly | FAIL — needed guidance | SKIP — not applicable this cycle}
- **Learning objective**: {what pattern/concept this cycle teaches}
- **Test output**: {vitest output summary — PASS or FAIL with details}
- **All tests passing**: YES | NO
- **Interview evidence**: {1-2 concrete observations from this cycle tied to code, tests, or checkpoint responses}
- **No-hire triggers observed**: {none | list trigger names with cycle evidence}
- **Next step**: {what the learner should implement next | waiting for learner response | DONE}
- **Research needed**: {question for Researcher, or "none"}
```

## Completion Criteria

The TDD loop is DONE when:

1. All examples from the problem statement have corresponding tests
2. Key edge cases are covered (empty input, single element, boundaries)
3. All tests pass
4. The solution is correct and handles the problem's constraints
5. At least 4-5 meaningful test cycles have been completed

### Additional Coach Mode Criteria

1. The learner wrote ALL implementation code (Coach only inserted TODO comments)
2. All TODO comments have been removed from the final implementation file
3. The learner successfully completed at least one pattern checkpoint (stated the correct pattern and invariant)
4. The learner completed at least one Level 2+ test-ownership cycle (preferably at least one Level 3 cycle)

## Conventions

### File Naming

- Directory: `{4-digit-number}-{kebab-case-name}` (e.g., `0001-two-sum`)
- Implementation: `{kebab-case-name}.ts` (e.g., `two-sum.ts`)
- Test: `{kebab-case-name}.test.ts` (e.g., `two-sum.test.ts`)
- README: `README.md`
- Post-mortem: `POST_MORTEM.md`

### Branch Naming

- `tdd/{4-digit-number}-{kebab-case-name}` (e.g., `tdd/0015-3sum`)

### Commit Message

- `feat: solve {Problem Name} (#{number}) via TDD`

### Test Command

- `npx vitest run {test-file} --reporter=verbose`
- Never use watch mode during TDD cycles (use single-run mode)

### Markdown Standards

All `.md` files created during TDD must use **pure markdown formatting**:

- **No HTML elements**: Do not use `<p>`, `<code>`, `<strong>`, `<em>`, `<pre>`, `<ul>`, `<li>`, `<ol>`
- **Allowed HTML** (per `.markdownlint.json`): `<details>`, `<summary>`, `<sup>`, `<sub>`, `<br>`, `<img>`
- **Convert LeetCode HTML to markdown**:
  - `<strong>text</strong>` → `**text**`
  - `<em>text</em>` → `*text*`
  - `<code>text</code>` → `` `text` ``
  - `<pre>...</pre>` → fenced code blocks (```)
  - `<ul><li>` → markdown lists (`-`)
  - `<p>` → blank lines between paragraphs
  - `<sup>N</sup>` → `^N` or use `<sup>N</sup>` (allowed)
- **Validation**: Run `npx markdownlint {file}` on every markdown file before committing
- **Auto-fix**: Use `npx markdownlint --fix {file}` for auto-fixable violations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pertrai1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
