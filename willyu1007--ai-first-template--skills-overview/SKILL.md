---
name: skills-overview
description: Skill discovery system overview. Keywords: skills, discovery, overview. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Skills Overview

This document defines what 鈥渟kills鈥?mean in this template, how skill discovery works across providers, and where the SSOT lives.

---

## 1. What are skills?

In this template, a **skill** is a **routable, reusable guidance bundle**:
- domain-specific guidelines and best practices
- reusable patterns and small examples
- anti-patterns to avoid
- links to supporting docs/scripts/templates

Skills are designed for progressive disclosure: a skill entrypoint stays lightweight and points to deeper references only when needed.

---

## 2. Skills vs workflows

- **Skills**: 鈥渉ow to do things correctly鈥?guidance you apply while implementing.
- **Workflows**: step-by-step playbooks you follow to execute a multi-step process safely.

Rule of thumb:
- Use a **skill** when you want in-context guidance while coding.
- Use a **workflow** when you need checkpoints, validation, and explicit gates.

---

## 3. Where skills live (SSOT)

### 3.1 SSOT skill packages

Each skill has a single SSOT entrypoint under:

```text
/.system/skills/ssot/**/<skill-name>/SKILL.md
```

Supporting files should live next to the SSOT entrypoint (recommended) under supporting files, `examples/`, `scripts/`, and `templates/`.

### 3.2 Provider wrappers (generated)

Provider-specific entrypoints are **generated** thin wrappers:

```text
/.codex/skills/<skill-name>/SKILL.md
/.claude/skills/<skill-name>/SKILL.md
```

Wrappers MUST NOT be hand-edited.

To regenerate wrappers from SSOT:

```bash
python scripts/devops/skills/sync_skills.py --regenerate --target repo --publish-set repo_minimal
python scripts/devops/skills/sync_skills.py --regenerate --target integration --publish-set integration_default
```

To validate in CI / locally:

```bash
python scripts/devops/skills/sync_skills.py --check --target repo --publish-set repo_minimal
python scripts/devops/skills/sync_skills.py --check --target integration --publish-set integration_default
```

---

## 4. Trigger mechanism (discovery)

Primary mechanism:
- the `description` field in each SSOT `SKILL.md` drives provider-native skill discovery.

Optional enhancement:
- lightweight prompt-time suggestions via `/.system/skills/config/` (e.g. keyword-based hints), kept minimal to avoid reintroducing a full routing system.

---

## 5. Available skills (current template)

- `repo-routing` -> `/.system/skills/ssot/repo/repo-routing/SKILL.md`
- `architecture-core-mechanisms` -> `/.system/skills/ssot/repo/architecture-core-mechanisms/repo-architecture-overview/SKILL.md`
- `documentation-conventions` -> `/.system/skills/ssot/repo/documentation-conventions/documentation-overview/SKILL.md`
- `scaffolding` -> `/.system/skills/ssot/repo/scaffolding/new-module/SKILL.md`
- `workflow-library` -> `/.system/skills/ssot/repo/workflow-library/workflows-overview/SKILL.md`
- `execution-plans` -> `/.system/skills/ssot/repo/execution-plans/execution-plan-authoring/SKILL.md`
- `skill-developer` -> `/.system/skills/ssot/repo/skill-developer/skill-development-patterns/SKILL.md`
- `backend-dev-guidelines` -> `/.system/skills/ssot/backend/backend-dev-guidelines/SKILL.md`
- `frontend-dev-guidelines` -> `/.system/skills/ssot/frontend/frontend-dev-guidelines/SKILL.md`
- `route-tester` -> `/.system/skills/ssot/repo/route-tester/route-testing-patterns/SKILL.md`
- `error-tracking` -> `/.system/skills/ssot/repo/error-tracking/error-tracking-patterns/SKILL.md`

---

## 6. How to add a new skill (short workflow)

1. Create `/.system/skills/ssot/<group>/<skill-name>/SKILL.md`.
2. Ensure front matter includes:
   - `name`: must match `<skill-name>` (kebab-case, <= 64 chars)
   - `description`: single-line, <= 500 chars, include trigger keywords
3. Add supporting files (optional) under the same skill directory (recommended under supporting files and `examples/`).
4. Regenerate and validate:
   - `python scripts/devops/skills/sync_skills.py --regenerate --target repo --publish-set repo_minimal`
   - `python scripts/devops/skills/sync_skills.py --check --target repo --publish-set repo_minimal`
   - `python scripts/devops/skills/sync_skills.py --regenerate --target integration --publish-set integration_default`
   - `python scripts/devops/skills/sync_skills.py --check --target integration --publish-set integration_default`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
