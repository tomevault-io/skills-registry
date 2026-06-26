---
name: dde-ext-verification-loop
description: DDE subordinate extension. Execute verification loop (build/type/lint/test/security/diff) and return a structured quality report. Use when this capability is needed.
metadata:
  author: LeslieLinXinxiang
---

# DDE Extension: Verification Loop

Skill Version: v0.1

## Authority Chain
- Commander: `dde-bootstrap`
- Write gate: `dde-code-guard`
- This skill executes checks but does not own requirement authority.

## When to Use
- After significant implementation.
- Before PR/merge checkpoint.
- When user asks for quality gate status.

## Execution Scope
Allowed:
- Run build/test/lint/type/security/diff commands
- Collect failures and classify severity
- Provide concrete fix plan

Not allowed:
- Directly editing code to fix failures without approval gate
- Altering Layer docs as authority source

## Verification Phases
1. Build
2. Type check (if applicable)
3. Lint/style check (if applicable)
4. Tests + coverage
5. Security quick scan
6. Diff review

## Gate Rule
If auto-fix is requested or needed:
- Emit `DDE_EXTENSION_HANDOFF_REQUIRED`
- Provide target files and minimal patch intent
- Stop and wait for `dde-code-guard` `[APPROVED]` flow

## Output Contract
Produce:
- `VERIFICATION REPORT`
- PASS/FAIL per phase
- Overall status: READY / NOT READY
- Actionable fix list

---
> Source: [LeslieLinXinxiang/dde-agent-skills](https://github.com/LeslieLinXinxiang/dde-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
