---
name: audit-plan
description: | Use when this capability is needed.
metadata:
  author: Lbstrydom
---

# Plan Audit Loop

Iteratively refine a plan with GPT-5.4 + Gemini final review until findings
plateau, then gate with the independent reviewer.

**Input**: `$ARGUMENTS` — either a plan file path (PLAN_AUDIT) or a task
description with no path (PLAN_CYCLE: generate-then-audit).

---

## Step 0 — Parse Input and Validate

| Input | Mode |
|---|---|
| `<plan-file>` | PLAN_AUDIT — audit existing plan iteratively |
| `<task description>` (no path) | PLAN_CYCLE — generate plan, then audit |

Validate: `OPENAI_API_KEY` is set. `GEMINI_API_KEY` for Step 6 (falls back to
Claude Opus when absent). `SUPABASE_AUDIT_URL` for cloud learning (optional).

Initialise session ID: `SID=audit-plan-$(date +%s)`.

Show kickoff card:
```
═══════════════════════════════════════
  /audit-plan — [MODE] — Starting
  Plan: <path> | Max 3 rounds | SID: $SID
═══════════════════════════════════════
```

---

## Step 1 — Plan Generation (PLAN_CYCLE only)

Generate the plan with `/plan` (the unified planner — auto-detects scope
as backend / frontend / full-stack). Save to `docs/plans/<name>.md`.
Skip for PLAN_AUDIT.

`/plan-backend` and `/plan-frontend` are deprecated thin aliases that
inject `--scope=backend|frontend` into `/plan`; either entry-point
works. The output is one consolidated plan document regardless of
scope, so this step always produces a single file to audit (no
child-plan merging like the old flow).

---

## Step 2 — Run Plan Audit

```bash
node scripts/openai-audit.mjs plan <plan-file> --mode plan \
  --out /tmp/$SID-r1-result.json \
  2>/tmp/$SID-r1-stderr.log
```

**Critical**: always pass `--mode plan`. Without it, Gemini in Step 6 can flag
absent implementations (the plan describes work that doesn't exist yet, which
is by design for plan-audit).

### Round 2+ invocation

R2+ mode injects prior rulings as system-prompt exclusions and applies
post-output suppression against the ledger.

```bash
node scripts/openai-audit.mjs plan <plan-file> --mode plan \
  --round 2 \
  --ledger /tmp/$SID-ledger.json \
  --out /tmp/$SID-r2-result.json \
  2>/tmp/$SID-r2-stderr.log
```

Plan audit is single-file — no `--passes`, `--diff`, or `--changed` plumbing
needed (those are code-audit concerns).

### Show results

```
═══════════════════════════════════════
  ROUND 1 PLAN AUDIT — SIGNIFICANT_ISSUES
  H:4 M:7 L:2 | Cost: ~$0.18
  Top: [H1] Missing failure mode for X
═══════════════════════════════════════
```

---

## Step 3 — Triage (validity × scope × action)

For each finding, record three orthogonal judgements:

| Dimension | Values | Meaning |
|---|---|---|
| **validity** | `valid` / `invalid` / `uncertain` | Is the concern real for THIS plan? |
| **scope** | `in-scope` / `out-of-scope` | Does it cite a section the plan owns? |
| **action** | `fix-now` / `defer` / `dismiss` / `rebut` | What happens next? |

### Triage rules

- `validity=invalid` → action MUST be `dismiss` or `rebut`
- `validity=uncertain` → action MUST be `rebut` (GPT deliberation)
- `validity=valid` + `scope=in-scope` + HIGH/MEDIUM → `fix-now`
- `validity=valid` + `scope=out-of-scope` → `defer` to "Out of Scope (Future)" plan section
- `validity=valid` + `scope=in-scope` + LOW → operator choice

### Tiered rebuttal

| Severity | Deliberation |
|---|---|
| HIGH | ALWAYS send to GPT deliberation |
| MEDIUM | ALWAYS send to GPT deliberation |
| LOW | Claude decides locally |

Only send rebuttal if rebut HIGH or MEDIUM findings exist:

```bash
node scripts/openai-audit.mjs rebuttal <plan-file> <rebuttal-file> \
  --out /tmp/$SID-resolution.json 2>/tmp/$SID-rebuttal-stderr.log
```

### Convergence — early-stop on rigor pressure

**Plan audits have infinite refinement surface** — after round 2-3, findings
shift from "real design bugs" to "push for more rigor". Stop early.

**Max 3 rounds** unless HIGH count is actively decreasing:

| Condition | Action |
|---|---|
| R1 → R2 HIGH count drops >30% | Continue to R3 |
| R2 → R3 HIGH count drops significantly | Continue to R4 (rare) |
| HIGH count plateaus or increases | **STOP** — remaining findings are scope pressure |
| R2+ findings push for v2 features, parser deps | **STOP** — record as "Out of Scope" |

When stopping with deferrals, append a `## Out of Scope (Future)` section to
the plan listing deferred concerns with rationale.

**Step 6 (Gemini final review) is MANDATORY** after the last audit round,
regardless of convergence — except when both `GEMINI_API_KEY` and
`ANTHROPIC_API_KEY` are absent.

---

## Step 3.5 — Update Adjudication Ledger

After each deliberation round, write ledger entries for every finding before
proceeding to Step 4. The ledger drives R2+ rulings injection and post-output
suppression.

Full writer invocation example + status field semantics: `references/ledger-format.md`.

---

## Execution order

**Wait for rebuttal BEFORE editing the plan.**

1. Send rebuttal (if rebut HIGH/MEDIUM findings from triage)
2. Wait for rebuttal response
3. Write adjudication ledger (Step 3.5)
4. Edit plan (Step 4)
5. Re-audit (Step 5)

---

## Step 4 — Edit Plan

Plans are single files — apply fixes via `Edit` tool. ALL HIGH must be
addressed (fix or defer-with-rationale). MEDIUM until ≤2 remain. LOW
optional.

```
═══════════════════════════════════════
  EDITING PLAN — 11 findings
  Fixed in plan: 8
  Deferred to "Out of Scope": 2 (with rationale)
  Dismissed (LOW, low-leverage): 1
═══════════════════════════════════════
```

After editing, update ledger entries to `remediationState: 'fixed'` for
fixed items.

---

## Step 5 — Verify and Loop (R2+)

After edits, re-audit with R2+ mode (back to Step 2):

1. Use the same plan file path.
2. Pass `--round <N>` and `--ledger /tmp/$SID-ledger.json`.
3. Track finding churn using `_hash` fields: resolved / recurring / new.

Stop per the rigor-pressure rule above (max 3 rounds unless HIGH dropping).

---

## Step 6 — Gemini Independent Review (MANDATORY)

Run Gemini 3.1 Pro as the final gate. Falls back to Claude Opus when
`GEMINI_API_KEY` is absent.

```bash
node scripts/gemini-review.mjs review <plan-file> /tmp/$SID-transcript.json \
  --out /tmp/$SID-gemini-result.json 2>/tmp/$SID-gemini-stderr.log
```

Verdict handling: `APPROVE` → done. `CONCERNS` → deliberate on findings, edit
plan, re-run Gemini. `REJECT` → present to user with recommendation.

Full transcript-building, verdict routing, deliberation protocol, and
category-error handling: `references/gemini-gate.md`.

---

## UX Rules

1. Status card after every phase
2. Never dump raw JSON — parse and summarise
3. Show every plan edit with file + line reference
4. Cost tracking: `cost ≈ (input × 2.5 + output × 10) / 1M`
5. Batch all user decisions into one prompt

## Key Principles

1. **Peer relationship** — neither model blindly defers
2. **Three-model system** — Claude (author) + GPT-5.4 (auditor) + Gemini (final arbiter)
3. **Stop at rigor pressure** — max 3 rounds unless HIGH actively dropping
4. **Always `--mode plan`** — without it, Gemini flags absent implementations
5. **No self-review** — Step 6 final gate reviews Claude-GPT transcript

---

## Reference files

This skill's canonical flow is above. The files below cover specialised
situations — read them only when the trigger applies.

| File | Summary | Read when |
|---|---|---|
| `references/ledger-format.md` | Adjudication ledger schema + writer invocation example for each finding outcome. | Step 3.5 — about to write ledger entries, OR diagnosing R2+ suppression misbehaviour. |
| `references/gemini-gate.md` | Step 7 Gemini independent review protocol — transcript, verdict handling, re-review loop. | Step 6 starting, OR Gemini returned CONCERNS/REJECT and need deliberation rules. |

---
> Source: [Lbstrydom/claude-engineering-skills](https://github.com/Lbstrydom/claude-engineering-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
