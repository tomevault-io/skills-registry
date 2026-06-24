---
name: audit
description: > Use when this capability is needed.
metadata:
  author: lvergro
---

# /audit — Security Audit

Read `.claude/models.yml` for model routing. This skill uses model: sonnet.

## Scope
Input: directory path, module name, or blank for full codebase. Examples: `src/auth`, `payments`, `/audit`.
Audit: $ARGUMENTS (or full codebase if no arguments)

## Flow
1. Read `.claude/project.yml` → invariants and critical_flows
2. Read `.claude/memory/architecture.md` → security patterns
3. Analyze codebase for:
   - Tenant isolation violations (missing tenant column filters)
   - Auth bypasses (missing role checks)
   - Input validation gaps (SQL injection, XSS)
   - Hardcoded secrets or credentials
   - RLS policy gaps
4. Generate report

## Output
- Summary in conversation
- Full report: `/docs/audits/[date]-audit-full.md`
- Action items: `/docs/audits/TODO.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lvergro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
