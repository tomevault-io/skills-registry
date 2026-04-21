---
name: verification-standards
description: Project-wide verification protocols, deployment procedures, and automation scripts. Use when this capability is needed.
metadata:
  author: nhomnhem
---

# Verification Standards

> **MANDATORY:** Apply these protocols before any PR, deployment, or final task completion.

---

## 🏁 Final Checklist Protocol

**Trigger:** When the user says "son kontrolleri yap", "final checks", "çalıştır tất cả test", or similar.

| Task Stage | Command | Purpose |
| :--- | :--- | :--- |
| **Manual Audit** | `python .agent/scripts/checklist.py .` | Priority-based project audit |
| **Pre-Deploy** | `python .agent/scripts/checklist.py . --url <URL>` | Full Suite + Performance + E2E |

**Priority Execution Order:**
1. **Security** → 2. **Lint** → 3. **Schema** → 4. **Tests** → 5. **UX** → 6. **Seo** → 7. **Lighthouse/E2E**

**Rules:**
- **Completion**: A task is NOT finished until `checklist.py` returns success.
- **Reporting**: If it fails, fix the **Critical** blockers first (Security/Lint).

---

## 🛠️ Available Automation Scripts

| Script | Skill | When to Use |
| :--- | :--- | :--- |
| `security_scan.py` | vulnerability-scanner | Always on deploy |
| `dependency_analyzer.py` | vulnerability-scanner | Weekly / Deploy |
| `lint_runner.py` | lint-and-validate | Every code change |
| `test_runner.py` | testing-patterns | After logic change |
| `schema_validator.py` | database-design | After DB change |
| `ux_audit.py` | frontend-design | After UI change |
| `accessibility_checker.py` | frontend-design | After UI change |
| `seo_checker.py` | seo-fundamentals | After page change |
| `bundle_analyzer.py` | performance-profiling | Before deploy |
| `mobile_audit.py` | mobile-design | After mobile change |
| `lighthouse_audit.py` | performance-profiling | Before deploy |
| `playwright_runner.py` | webapp-testing | Before deploy |

---

## 🚀 5-Phase Deployment Strategy

1. **Phase 1: Validation** → Run `lint_runner.py` and `security_scan.py`.
2. **Phase 2: Staging** → Deploy to internal branch/test set.
3. **Phase 3: Automated Testing** → Run `playwright_runner.py` and `test_runner.py`.
4. **Phase 4: Optimization Audit** → Run `lighthouse_audit.py` and `bundle_analyzer.py`.
5. **Phase 5: Production Release** → Final merge and smoke test.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nhomnhem) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
