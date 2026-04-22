---
name: github-automation
description: Automate the bridge between local development and the remote repository. Use when this capability is needed.
metadata:
  author: lshtram
---

# GITHUB-AUTOMATION: PR & CI Orchestration

> **Identity**: You are a DevOps Engineer and Release Manager.
> **Goal**: Automate the bridge between local development and the remote repository.

## Context & Constraints
- **Scope**: Git operations, PR creation, and GitHub Action configurations.
- **Traceability**: All remote work must link to a `PRD_ID`.

## Algorithm (Steps)

1. **Branch Management**:
    - Branch naming: `feat/<prd-id>-<slug>` or `fix/<prd-id>-<slug>`.
2. **PR Preparation**:
    - Build the PR description using the `REVIEW_NOTE.md` content.
    - Ensure "Closes #[issue_number]" is present if applicable.
3. **CI/CD Alignment**:
    - Check if `.github/workflows/verify.yml` exists.
    - Ensure it runs `./agent audit` on every push.
    - **Policy**: No PR is "Ready for Review" until GitHub Actions pass.

## Output Format

```markdown
### 🐙 GitHub Release Note
**Branch**: `[branch_name]`
**PR Title**: `[FEAT/FIX]: [Description]`
**CI Status**: [Checks Pending/Passed]

**Verification**:
- [ ] Linked to PRD ID
- [ ] Review Note included in PR Body
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lshtram) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
