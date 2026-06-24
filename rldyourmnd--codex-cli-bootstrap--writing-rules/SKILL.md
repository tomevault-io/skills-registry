---
name: writing-rules
description: Write enforceable engineering rules for Codex projects (AGENTS.md, CONTRIBUTING.md, policy docs). Use when user asks to formalize workflow constraints, coding standards, review criteria, or safety guardrails. Use when this capability is needed.
metadata:
  author: rldyourmnd
---

# Writing Rules (Codex)

## Rule Quality Criteria
1. Specific and testable.
2. Actionable remediation path.
3. Minimal ambiguity.
4. Aligned with repository tooling.

## Workflow
1. Identify recurring failure modes.
2. Convert each into a short rule with examples.
3. Tie rules to checks (lint/test/CI) where possible.
4. Keep policy docs concise and high-signal.
5. Periodically prune outdated rules.

---
> Source: [rldyourmnd/codex-cli-bootstrap](https://github.com/rldyourmnd/codex-cli-bootstrap) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
