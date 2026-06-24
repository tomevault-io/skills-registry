---
name: code-audit
description: Use when wanting an independent code review by a different LLM (Codex CLI primary, Gemini fallback). Audits implementation against ai-rules.md and architecture.md. Surfaces critical bugs, security issues, rule violations, and performance concerns. Confirms before adding findings to Kanban.
metadata:
  author: ricardojpalves
---

# /code-audit

## Overview

Adversarial code review by a different LLM. Different model = genuinely different perspective. Catches issues Claude wrote and Claude wouldn't critique on its own.

Default auditor: **Codex CLI**. Fallback: Gemini CLI. Manual paste mode if neither installed.

## When to use
- After implementing a feature, before committing
- Before merging to main
- Before deploying
- Mid-build sanity check on critical code (auth, payments, data layer)

## When not to use
- Trivial changes (typo fixes, docs)
- Code Claude hasn't written yet
- Without ai-rules.md and architecture.md in place

## Pre-flight check

Before running, verify Codex CLI is installed:

```bash
which codex || echo "Codex CLI not installed"
```

If not installed:
1. Suggest install: `brew install openai-codex` (or current install method — check Codex docs)
2. Offer fallback: Gemini CLI (`which gemini`)
3. Last resort: manual paste mode (output the prompt, user pastes into ChatGPT/Codex web)

## Configuration

Read from `code-audit.config.md` in project root if present, else use defaults:

```yaml
auditor: codex            # codex | gemini | manual
focus:                    # what to weight heaviest
  - security
  - rule-compliance
  - performance
severity-threshold: 🟡    # minimum severity to surface
auto-create-kanban-tasks: false  # always confirm first
```

## Workflow

```
1. Determine scope
   → Default: diff since last commit
   → Or: /code-audit since=last-merge
   → Or: /code-audit file=src/auth.ts
   → Or: /code-audit dir=src/api/

2. Gather context
   → Read ai-rules.md (the non-negotiables to check against)
   → Read architecture.md (the structure to verify)
   → Get the diff or files in scope

3. Build adversarial prompt
   → Frame as "audit code someone else wrote"
   → Include ai-rules.md + architecture excerpt
   → Include the code being reviewed
   → Ask for: bugs, security, perf, rule violations, design smell, dead code

4. Call the auditor
   → Bash: codex review --prompt-file /tmp/audit-prompt.md
   → Capture structured response

5. Parse + present findings
   → Group by severity (🔴 critical / 🟡 important / 🟢 suggestion / ✅ verified-good)
   → Show: file:line, issue, suggested fix

6. Confirm before action
   → "Want me to fix the 🔴 critical issues now?"
   → "Want me to add findings as Kanban tasks?"
   → Default: NO auto-action without explicit yes
```

## Adversarial prompt template

```
You are auditing code written by another developer. The developer claims it's
done, but you suspect it has issues. Find them.

## Project rules (non-negotiable):
[paste ai-rules.md contents]

## Architecture context:
[paste architecture.md contents — relevant sections only]

## Code under review:
[paste diff or files]

## Your task:
Find bugs, security holes, rule violations, performance issues, design smells,
and dead code. Be specific: cite file:line. Suggest fixes.

For each finding, output:
- Severity: 🔴 critical / 🟡 important / 🟢 suggestion
- File:line
- What's wrong
- Why it matters
- Suggested fix

Also output a "✅ Verified good" section listing things you explicitly checked
and confirmed are correct. This shows what you reviewed.

Be direct. Don't soften critique. Don't be polite. The code is broken — find why.
```

## Output format

```
🔍 Code audit by [Codex / Gemini]
📅 Reviewed [N] files, [M] lines (since [reference])
⏱️  Took [X] seconds

🔴 Critical (X)
  • file.ts:42 — [issue]
    Why: [impact]
    Fix: [suggestion]

🟡 Important (X)
  • file.tsx:88 — [issue]
    [...]

🟢 Suggestions (X)
  • file.ts:18 — [issue]

✅ Verified good (X)
  • [Thing checked, no issues]

📋 Next step:
  → Fix 🔴 critical issues now? (y/n)
  → Add 🔴 findings to Kanban? (y/n)
  → Log audit to memory.md? (y/n)
```

## Cost & latency notes

- Codex CLI: ~30s–2min for a meaningful diff. Paid API call.
- Gemini CLI: similar.
- Don't run on every save. Run on demand or pre-commit / pre-deploy.

## Common mistakes

| Mistake | Fix |
|---|---|
| Auto-fixing without showing findings first | Always present findings, confirm before acting |
| Auto-creating Kanban tasks | Confirm first — false positives shouldn't pollute Kanban |
| Running on full repo every time | Default to diff-since-last-commit unless explicitly broader |
| Ignoring ai-rules.md | Rules are the non-negotiables — they ARE the audit criteria |

---
> Source: [ricardojpalves/product-workflow](https://github.com/ricardojpalves/product-workflow) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
