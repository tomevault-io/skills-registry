---
name: security-best-practices
description: Perform language and framework specific security best-practice reviews and suggest improvements. Trigger only when the user explicitly requests security best practices guidance, a security review/report, or secure-by-default coding help. Trigger only for supported languages (python, javascript/typescript, go). Do not trigger for general code review, debugging, or non-security tasks. Use when this capability is needed.
metadata:
  author: hebertzhu
---

# security-best-practices

## Intent
- Perform language and framework specific security best-practice reviews and suggest improvements. Trigger only when the user explicitly requests security best practices guidance, a security review/report, or secure-by-default coding help. Trigger only for supported languages (python, javascript/typescript, go). Do not trigger for general code review, debugging, or non-security tasks.

## Default operating pattern
1. Identify assets, trust boundaries, attacker assumptions, and sensitive flows.
2. Inspect the current implementation or design surface that creates risk.
3. Rank issues by impact, likelihood, and exploitability.
4. Recommend the smallest safe-by-default changes that materially reduce exposure.
5. Note verification steps and any residual risk that still remains.

## Pack fit
- Included in: `release-quality`, `security-quality`
- Keep examples generic, privacy-safe, and portable across hosts.

## Boundary
- Do not relabel a generic code review as a security review without actual security scope.

---
> Source: [hebertzhu/agent-foundry](https://github.com/hebertzhu/agent-foundry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
