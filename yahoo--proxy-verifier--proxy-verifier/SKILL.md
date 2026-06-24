---
name: apply-pr-comments
description: Evaluate current PR review comments, apply valid changes, and prepare suggested replies without posting them. Use when this capability is needed.
metadata:
  author: yahoo
---

# Evaluate and Apply Review Comments

Using `gh`, read review comments on the current PR. Evaluate which comments
should drive code changes and which should be answered without changes.

- Do not post replies on GitHub. Propose suggested replies in your report.
- Apply code changes for valid review feedback.
- Build using `../build-pv/SKILL.md`.
- Run relevant tests via `../run-unit-tests/SKILL.md` and `../run-autests/SKILL.md` when applicable.
- Do not commit or push. Leave changes unstaged for manual review.

---
> Source: [yahoo/proxy-verifier](https://github.com/yahoo/proxy-verifier) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
