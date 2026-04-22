---
name: security-audit
description: Procedure for analyzing code or dependencies for vulnerabilities Use when this capability is needed.
metadata:
  author: cpa03
---

## Procedure

1. Run `npm audit`.
2. Scan for hardcoded secrets using `grep`.
3. Review authentication/authorization logic in changed files.
4. Check for injection risks (SQLi, XSS) in inputs.
5. Report findings to `docs/findings.md` or fix if critical.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cpa03) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
