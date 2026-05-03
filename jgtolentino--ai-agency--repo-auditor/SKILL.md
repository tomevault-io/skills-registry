---
name: repo-auditor
description: Audit Odoo+DevOps repos for security, reliability, OCA compliance, and CI/CD quality. Output a concise markdown report + strict JSON. Use when this capability is needed.
metadata:
  author: jgtolentino
---

# Operating Method
1) Read repo_tree; create a Suspicion Map.
2) Analyze in order: Odoo addons → Docker/Compose → CI (GH Actions/GitLab) → Infra (Terraform/Ansible) → Observability → Docs.
3) Drive checks using ./checklists/*.md.
4) Each finding must include severity, file:line (when possible), short rationale, and a minimal fix.
5) Merge duplicates; keep ≤ 40 findings; mark assumptions if context is missing.
6) Render report with templates/report.md.gotmpl and validate JSON against templates/findings.json.schema.

# Severity
critical | high | medium | low.

# Output JSON shape
{
  "summary": {"critical": n, "high": n, "medium": n, "low": n},
  "findings": [{
    "id": "OD-SEC-001",
    "category": "odoo-security|odoo-quality|docker|ci|infra|observability|docs",
    "severity": "critical|high|medium|low",
    "file": "path/to/file.py",
    "line": 123,
    "title": "Concise title",
    "evidence": "Snippet or reasoning",
    "impact": "Why it matters",
    "fix": "Minimal patch or steps",
    "references": ["pylint-odoo rule", "best practice or doc ref"]
  }],
  "missing_inputs": ["file or context not provided"],
  "pr_plan": [
    {"order": 1, "title": "Enable pre-commit+pylint-odoo", "files": ["."], "est": "1h"},
    {"order": 2, "title": "Harden Dockerfile (non-root, healthcheck)", "files": ["Dockerfile"], "est": "2h"}
  ]
}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jgtolentino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
