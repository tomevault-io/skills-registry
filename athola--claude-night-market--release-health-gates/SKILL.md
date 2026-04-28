---
name: release-health-gates
description: Standardize release approvals with GitHub-aware checklists and deployment gate validation Use when this capability is needed.
metadata:
  author: athola
---
# Release Health Gates

## Purpose

Standardize release approvals by expressing gates as GitHub-aware checklists. Ensure code, docs, comms, and observability items are green before deployment.

## Gate Categories

1. **Scope & Risk** – Are all blocking issues closed or deferred with owners?
2. **Quality Signals** – Are required checks, tests, and soak times satisfied?
3. **Comms & Docs** – Are docs merged and release notes posted?
4. **Operations** – Are runbooks, oncall sign-off, and rollback plans ready?

## Workflow

1. Load skill to access gate modules.
2. Attach Release Gate section to deployment PR.
3. Use tracker data to auto-fill blockers and highlight overdue tasks.
4. Update comment as gates turn green; require approvals for any waivers.

## Outputs

- Release Gate markdown snippet (embed in PR/issue).
- QA Handshake summary referencing GitHub Checks.
- Rollout scorecard that persists in tracker data for retros.

## Exit Criteria

- All release gates evaluated and documented.
- Any blocking gates have waiver approvals recorded.
- Deployment PR contains embedded Release Gate snippet.
- Rollout scorecard saved for post-release retrospective.
## Troubleshooting

### Common Issues

**Command not found**
Ensure all dependencies are installed and in PATH

**Permission errors**
Check file permissions and run with appropriate privileges

**Unexpected behavior**
Enable verbose logging with `--verbose` flag

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/athola) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
