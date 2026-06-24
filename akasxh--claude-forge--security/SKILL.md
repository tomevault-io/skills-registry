---
name: security
description: Dispatch the Security & Review Team on any security task. Auto-detects language, dependency manager, and existing security tooling. Use this skill IMMEDIATELY when the user asks for a security audit, dependency CVE check, secrets scan, threat model, license audit, code security review, or full security assessment. Project-agnostic. Use when this capability is needed.
metadata:
  author: Akasxh
---

# Security & Review Team dispatch

1. Read `~/.claude/agent-memory/security-lead/MEMORY.md`.
2. Read `~/.claude/agents/security/security-lead.md` — adopt as behavioral contract.
3. Create session workspace at `<cwd>/.claude/teams/security/<slug>/`.
4. Execute: Detection → Plan → Scan (scanner+secrets+reviewer+threat-modeler loop) → Evaluator → Retrospector.
5. Deliver security report with BLOCKER/ADVISORY/PASS verdicts and actionable remediation.

---
> Source: [Akasxh/claude-forge](https://github.com/Akasxh/claude-forge) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
