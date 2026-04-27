---
name: moai-foundation-git
description: GitFlow automation and PR policy enforcement for MoAI-ADK workflows. Use when this capability is needed.
metadata:
  author: kivo360
---

# Foundation Git Skill

## Skill Metadata

| Field | Value |
| ----- | ----- |
| **Skill Name** | moai-foundation-git |
| **Version** | 2.0.0 (2025-10-22) |
| **Allowed tools** | Read (read_file), Bash (terminal) |
| **Auto-load** | On demand when keywords detected |
| **Tier** | Foundation |

---

## What It Does

GitFlow automation and PR policy enforcement for MoAI-ADK workflows.

**Key capabilities**:
- ✅ Best practices enforcement for foundation domain
- ✅ TRUST 5 principles integration
- ✅ Latest tool versions (2025-10-22)
- ✅ TDD workflow support

---

## When to Use

**Automatic triggers**:
- Related code discussions and file patterns
- SPEC implementation (`/alfred:2-run`)
- Code review requests

**Manual invocation**:
- Review code for TRUST 5 compliance
- Design new features
- Troubleshoot issues

---

## Tool Version Matrix (2025-10-22)

| Tool | Version | Purpose | Status |
|------|---------|---------|--------|
| **Git** | 2.47.0 | Primary | ✅ Current |
| **GitHub CLI** | 2.63.0 | Primary | ✅ Current |

---

## v0.17.0: Three Flexible Git Workflows

MoAI-ADK now supports **3 configurable SPEC development workflows** via `git.spec_git_workflow` setting:

### 1️⃣ Feature Branch + PR (feature_branch)

**Best for**: Team collaboration, code review, quality gates

```bash
# Create feature branch from develop
git checkout develop
git checkout -b feature/SPEC-001

# Implement with TDD: RED → GREEN → REFACTOR
git commit -m "🔴 RED: test description"
git commit -m "🟢 GREEN: implementation description"
git commit -m "♻️ REFACTOR: improvement description"

# Create Draft PR
gh pr create --draft --base develop --head feature/SPEC-001

# After implementation complete
git push origin feature/SPEC-001
gh pr ready
gh pr merge --squash --delete-branch
```

**Advantages**:
- ✅ Code review before merge
- ✅ CI/CD validation
- ✅ Team discussion
- ✅ Audit trail

**Disadvantages**:
- ⏱️ Slower than direct commit
- 📋 Requires PR review

---

### 2️⃣ Direct Commit to Develop (develop_direct)

**Best for**: Rapid development, solo/trusted developers

```bash
# Work directly on develop
git checkout develop

# Implement with TDD: RED → GREEN → REFACTOR
git commit -m "🔴 RED: test description"
git commit -m "🟢 GREEN: implementation description"
git commit -m "♻️ REFACTOR: improvement description"

# Push to develop (no PR)
git push origin develop
```

**Advantages**:
- ⚡ Fastest path to integration
- 📝 Minimal overhead
- 🚀 Suitable for rapid prototyping

**Disadvantages**:
- ⚠️ No review gate (requires trust)
- 📊 Less audit trail

---

### 3️⃣ Ask Per SPEC (per_spec)

**Best for**: Mixed team (hybrid review + direct approach)

**Behavior**: When creating each SPEC with `/alfred:1-plan`, Alfred asks:

```
"Which git workflow for this SPEC?"

Options:
- 📋 Feature Branch + PR (recommended for team)
- ⚡ Direct Commit to Develop (recommended for speed)
```

**Advantages**:
- 🎯 Flexibility per feature
- 👥 Team can choose per SPEC
- 🔄 Combine both approaches

**Disadvantages**:
- 🤔 Manual decision per SPEC
- ⚠️ Inconsistency if overused

---

## Configuration

**Set workflow in `.moai/config.json`**:

```json
{
  "git": {
    "spec_git_workflow": "feature_branch"
  }
}
```

**Valid values**:
- `"feature_branch"` - Always use PR workflow
- `"develop_direct"` - Always direct commit
- `"per_spec"` - Ask user for each SPEC

---

## Inputs

- Git configuration from `.moai/config.json`
- SPEC metadata for branch naming
- TDD phase information (RED/GREEN/REFACTOR)

## Outputs

- Feature branch creation commands
- Commit messages with @TAG references
- PR status transitions
- GitHub CLI commands for automation

## Failure Modes

- When Git is not installed
- When invalid spec_git_workflow value in config
- When PR merge conflicts occur
- When CI/CD checks fail

## Dependencies

- Access to project files via Read/Bash tools
- Git 2.47.0+ installed
- GitHub CLI 2.63.0+ for PR operations
- Integration with `moai-alfred-config-schema` for config reading
- Integration with `moai-foundation-trust` for quality gates

---

## References

- **Feature Branch Workflow**: SKILL.md "v0.17.0: Three Flexible Git Workflows"
- **Direct Commit Workflow**: SKILL.md "v0.17.0: Three Flexible Git Workflows"
- **Per-SPEC Workflow**: SKILL.md "v0.17.0: Three Flexible Git Workflows"

_Documentation updated 2025-11-04_

---

## Changelog

- **v2.1.0** (2025-11-04): Added three configurable workflows (feature_branch, develop_direct, per_spec) and token management
- **v2.0.0** (2025-10-22): Major update with latest tool versions, comprehensive best practices, TRUST 5 integration
- **v1.0.0** (2025-03-29): Initial Skill release

---

## Works Well With

- `moai-alfred-config-schema` (workflow configuration)
- `moai-alfred-ask-user-questions` (per-spec workflow selection)
- `moai-foundation-trust` (quality gates)
- `moai-alfred-code-reviewer` (code review)
- `moai-essentials-debug` (debugging support)

---

## Best Practices

✅ **DO**:
- Read `spec_git_workflow` from config before creating branch
- Use feature branches for team projects
- Use direct commits for solo/prototype work
- Use per_spec for mixed team flexibility
- Maintain test coverage ≥85% regardless of workflow
- Document all public APIs

❌ **DON'T**:
- Skip quality gates based on workflow choice
- Use deprecated tools
- Ignore security warnings
- Mix testing frameworks
- Force workflow decisions on team members (use per_spec)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kivo360) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
