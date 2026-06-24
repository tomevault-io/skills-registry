---
name: gemini-code-auditor
description: | Use when this capability is needed.
metadata:
  author: mcevoyinit
---

# Gemini Code Auditor

## Problem
CLAUDE.md summaries and commit messages give Gemini insufficient context to find non-obvious
structural bugs. Line-level findings require feeding actual source code with precise framing.

## The Methodology

### Step 1: Read the target file(s) fully
Start with the root/entrypoint (e.g., `havona.py`, `app.py`, `server.ts`). Use `Read` tool.
Note key sections, line numbers, and structural patterns before writing the prompt.

### Step 2: Structure the prompt as numbered sections + questions

Each prompt section contains:
```
=== SECTION N: [method name] (lines X-Y, condensed) ===
[paste condensed code — keep line references, omit boilerplate]

Q[n]: [adversarial question with exact reference to suspicious pattern]
```

**Design context block** (put at top, ~150 chars):
```
DESIGN CONTEXT:
- What the namespaces mean (e.g., NS0=admin, NS1=identity, NS2+=company data)
- Auth assumptions (always required / optional / env-dependent)
- Dual-persistence rules (DGraph first, blockchain second)
- Critical background threads and their state dependencies
```

### Step 3: Ask adversarial questions

Good question patterns:
- **Cascade trace**: "If X fails at line N, trace the exact cascade. Which subsequent lines silently degrade?"
- **Assumption violation**: "Method M assumes Y. What is the exact failure scenario when Y is false?"
- **TOCTOU**: "Check A at line N happens before write B at line M. What is the race?"
- **Silent swallow**: "Exception caught at line N. What are the two distinct scenarios? Which is benign, which is not?"
- **Semantic conflation**: "Variable V is used for both meaning A and meaning B. What leaks?"

### Step 4: Specify the answer format

Always end with:
```
ANSWER FORMAT: For each Q, give: (1) exact diagnosis with line reference, (2) severity (P0/P1/P2),
(3) minimum fix in ≤3 lines of pseudo-code. Be adversarial. Don't soften.
```

### Step 5: Synthesize findings

Present back to user as:
- P0 table (production-critical, fix immediately)
- P1 table (correctness bugs, fix this sprint)
- P2 notes (improvements, address when touching the file)

For each finding: location + issue + minimum fix pseudo-code.

## What Gemini is Good at Finding

From empirical runs on Havona:
- **Init cascade failures**: GraphQL pool returns None → server announces ready with 100% 500 rate
- **Blocking startup ops**: Schema sync in `__init__` before Flask binds → crash on DGraph unavailable
- **Silent exception swallows**: Broad `except` catches both "NS doesn't exist" and "wrong password"
- **JWT/token expiry**: Static tokens stored at init with no refresh path → silent failure at T+expiry
- **TOCTOU state checks**: `can_transition` check before DGraph write → two threads both pass, second aborts
- **Namespace routing bugs**: Hardcoded `namespace=1` for data that lives in NS2+ → always returns None
- **Post-query client-side filters**: Semantic conflation of "scoping disabled" vs "user has no books"
- **Sequential gap assumptions**: `break` on first auth failure assumes contiguous IDs

## What Gemini Gets Wrong (False Positives Observed)

- **Token refresh already handled**: May flag JWT expiry if it doesn't see the pool's internal TokenCache
- **Health check semantics**: May flag `/status` always returning 200 without knowing it's an intentional diagnostics endpoint (not a K8s probe)
- **DGraph error messages**: Gemini sometimes suggests string-matching error messages that DGraph sanitizes — always use pre-flight queries instead

## Execution

```bash
python3 ~/.claude/skills/gemini/utils/gemini_query.py \
  "[full prompt with sections and questions]" \
  "[design context string]"
```

Timeout: 150-180 seconds for 9+ questions. Use `--max-time 120` in curl is already set.

## Workflow for Large Files

For files >500 lines, run two passes:
1. **Pass 1**: Init/startup sequence + auth + key routing decisions
2. **Pass 2**: Core endpoint handlers + health/monitoring + edge cases

Feed findings from Pass 1 as context in Pass 2.

## Recursive Tree Traversal

For deep audits:
1. Start with entrypoint (`havona.py`) — finds architectural risks
2. Gemini flags suspicious subsystems — drill into those next
3. Feed subsystem code with Gemini's prior findings as context
4. Stop when findings become repetitive or P2-only

## Session Output Template

After the Gemini run, produce a table:

```markdown
### P0 (fix immediately)
| Q | Location | Issue | Fix |
|---|----------|-------|-----|
| Q3 | auth_utils:748 | ... | ... |

### P1 (fix this sprint)
| Q | Location | Issue |
|---|----------|-------|
| Q1 | havona.py:140 | ... |
```

## Notes

- Always verify findings against actual code before adding to implementation plan
- Gemini's false positive rate was ~22% on Havona run (2/9 findings were pre-handled)
- The false positives were identified because we read the code first — don't skip Step 1
- Gemini's self-corrections (Q3: string matching → pre-flight query) are often better than initial suggestions

---
> Source: [mcevoyinit/agentic-skills](https://github.com/mcevoyinit/agentic-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
