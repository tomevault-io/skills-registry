---
name: review-plan
description: Stress-test a plan before acting on it. Finds blind spots, missing steps, and wishful thinking, then gives a clear verdict — APPROVE, or REVISE with a fixed-up plan. Substantive plans get a council of critics by default; `quick` or `single-pass` runs one fast review instead. Use right after plan mode, or on any plan file. Use when this capability is needed.
metadata:
  author: chrisblattman
---

# /review-plan — Stress-Test a Plan

Read the plan, critique it hard, deliver a verdict. For plans of any substance the default is a small council of independent critics with a separate synthesis — three reviewers with different lenses catch more than one. `quick` or `single-pass` opts down to a single fresh-context review.

## Step 1 — Find and read the plan

In priority order:
1. **Explicit file** — the `file:path` argument. If the path doesn't exist: "File not found: [path]. Check the path and try again."
2. **Plan-mode file** — the most recent file in `~/.claude/plans/` (Glob `~/.claude/plans/*.md`), when the user just finished plan mode.
3. **The conversation** — a plan developed or pasted in this session.

If no plan is found anywhere, reply: "No plan found. Run `/review-plan` right after plan mode, or `/review-plan file:path/to/plan.md`." and stop.

If the plan is under ~50 words, warn — "This plan is very brief; the review may be limited." — then proceed.

## Step 2 — Pick the review path and the reviewer

- **Council (default).** Any plan where a missed flaw would cost real time, money, or credibility: implementation plans, migrations, research designs, proposals, workflow changes.
- **Single-pass.** Only when the user says `quick` or `single-pass`, or the plan is short and low-stakes (a simple to-do sequence).

Assign an expert role for the critique — infer from the plan's content; the user's `role:"..."` always wins:

| The plan is about | Reviewer role |
|-------------------|---------------|
| Software, automation, AI tools | AI engineering specialist |
| Grants, proposals, funders | Research funding strategist |
| Papers, studies, data analysis | Research methodology specialist |
| Project management, workflows, operations | Operations specialist |
| Anything else | Strategic planning specialist |

Announce: "**Reviewing as:** meticulous [role]. (Say `role:\"...\"` to override.)"

## Step 3 — Research best practices (skip when `quick`)

Privacy check before any search: web search sends search terms outside Claude. If the plan includes confidential records, personal contact details, research-participant or human-subjects material, unpublished sensitive work, private financial/legal/medical/school details, or anything the user would not paste into a public website, skip web search and say: "I am skipping web search because the plan appears sensitive; reviewing from the local plan and general domain knowledge." If the plan is safe, run two web searches: `"[the plan's main approach] best practices"` and `"[the plan's domain] common pitfalls"`. Distill 3–5 principles that bear on THIS plan — they feed the critique. If web search is unavailable, continue and note: "Web research unavailable; reviewing from domain knowledge."

## Step 4 — Run the critique

### Council path (default)

Follow the bundled council skill — `${CLAUDE_PLUGIN_ROOT}/skills/council/SKILL.md` — with:
- **Panel:** the plan panel (skeptic, pre-mortem, completeness-checker). **Type:** plan.
- **Critic input:** the full plan text plus the best-practice principles from Step 3.
- **`--peer codex|gemini`:** pass it through if the user set it. The council skill checks whether that engine is actually available, asks for explicit send confirmation, and falls back to all-Claude with a friendly note if not. **Privacy check first:** a peer review sends the full plan to another AI service — if the plan contains confidential, personal, or research-participant material, don't pass `--peer`; tell the user why and run all-Claude.

The council skill owns the persona-file check, the parallel dispatch, and the mandatory separate synthesis. Don't re-implement those here.

### Single-pass path (`quick` / `single-pass` / trivial plan only)

Dispatch ONE Task call, `subagent_type: general-purpose` — a fresh context avoids grading your own homework — with this prompt:

```
You are a meticulous [role]. Find what's missing, what will break, and what's
wishful thinking. Do not rationalize or hedge.

PLAN TO REVIEW: [full plan text]
BEST PRACTICES CONTEXT: [principles from Step 3, or "none — quick mode"]

Review against 6 dimensions:
1. PRE-MORTEM — it's 3 months later and this failed. Top 3 causes?
2. COMPLETENESS — what's missing that a domain expert would expect?
3. FEASIBILITY — which steps depend on unconfirmed resources, approvals, or data?
4. BEST-PRACTICE FIT — where does the plan deviate, and is the deviation justified?
5. SEQUENCING — hidden blockers? Would reordering reduce risk?
6. SPECIFICITY — could someone unfamiliar execute each step? Flag hand-waves
   ("figure out", "as needed") and missing success criteria.

Classify each finding: Red (will likely cause failure or major rework), Yellow
(risky but survivable), Green (minor). End with
VERDICT: APPROVE | REVISE — [one-line reason]
```

## Step 5 — Present the review

```
════════════════════════════════════════
PLAN REVIEW — [plan title]
════════════════════════════════════════
Reviewing as: meticulous [role]  ·  Source: [file / plan mode / conversation]

STRENGTHS
1. [what the plan gets right — be specific]

WEAKNESSES & GAPS
🔴 [issue] → Fix: [specific change]
🟡 [issue] → Fix: [specific change]
🟢 [issue] → Fix: [specific change]

VERDICT
✅ APPROVE — [reason]   or   🔄 REVISE — [reason] + revised plan below
```

When the council ran: take the verdict, blockers, and patches from the synthesis output; include the per-critic one-line verdicts; put the raw critiques in a `<details>` block at the bottom. If the verdict is REVISE, draft the revised plan yourself from the blockers and patches, marking changed sections `[CHANGED]` and additions `[NEW]`.

## Step 6 — Iterate

Ask: "Apply these revisions, or give me feedback to refine further?"
- **Accept** → apply the revisions to the plan file (if file-based) or present the final version.
- **Feedback** → loop back to Step 4 once, with the user's notes added to the critic input.
- After 2 review cycles: "This plan has been reviewed [N] times — further rounds hit diminishing returns. Consider running it and iterating in practice."

## Examples

```
/review-plan                                  ← right after plan mode
/review-plan file:docs/migration-plan.md
/review-plan quick
/review-plan single-pass role:"clinical trial design specialist"
/review-plan --peer codex
```

---
> Source: [chrisblattman/claudeblattman](https://github.com/chrisblattman/claudeblattman) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-02 -->
