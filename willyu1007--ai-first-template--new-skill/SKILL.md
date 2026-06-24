---
name: new-skill
description: Scaffold a new SSOT skill package and regenerate provider wrappers. Keywords: skill, scaffold, SSOT. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Scaffold: New Skill Package

This document is the SSOT entrypoint for skill scaffolding.

---

## 1. Purpose & Scope

**Purpose**: Create a new SSOT skill package under `/.system/skills/ssot/**` and regenerate provider wrappers.

**Scope**:
- Creates a new skill directory containing `SKILL.md` (SSOT entrypoint)
- Optionally adds empty supporting directories (supporting files, `examples/`, etc.) if the scaffold includes them
- Regenerates wrappers under `/.codex/skills/**` and `/.claude/skills/**`
- Validates wrapper/SSOT consistency

**Out of scope**:
- Designing the full skill contents (done iteratively after scaffold)
- Adding new runtime hooks

---

## 2. Inputs & Preconditions

### Required Inputs

| Parameter | Type | Description |
|-----------|------|-------------|
| `skill_name` | string | Skill name (kebab-case, <= 64 chars) |
| `description` | string | Single-line discovery description (<= 500 chars; include trigger keywords) |

### Optional Inputs

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `group` | string | `repo` | Group directory under `/.system/skills/ssot/` (not itself a skill) |
| `scope` | enum | `repo` | `repo` or `module` scope |
| `module_id` | string | (none) | Required when `scope=module` |
| `skip_regenerate` | boolean | false | Skip provider wrapper regeneration after apply |

### Preconditions

1. Skill discovery SSOT exists: `/.system/skills/ssot/`
2. Wrapper generator is available: `scripts/devops/skills/sync_skills.py`
3. `skill_name` is not already present in SSOT

---

## 3. Step-by-Step Flow (AI + Human)

1. AI collects required parameters (`skill_name`, `description`) from user
2. AI runs `--dry-run` to preview changes
3. Human approves the plan
4. AI executes the scaffold
5. AI records results in workdocs

---

## 4. Tools & Scripts

| Tool | Purpose |
|------|---------|
| `scripts/devops/scaffold/new_skill.py` | Orchestrator script |
| `scripts/devops/skills/sync_skills.py` | Wrapper regeneration + consistency validation |

---

## 5. Outputs & Side Effects

### Files Created/Modified

```text
/.system/skills/ssot/<group>/<skill-name>/SKILL.md     # New SSOT entrypoint
/.codex/skills/<skill-name>/SKILL.md                   # Generated wrapper (Codex)
/.claude/skills/<skill-name>/SKILL.md                  # Generated wrapper (Claude)
```

---

## 6. Safety & Rollback

### Safety Measures

- Prefer `--dry-run` to preview new files/paths
- Do not hand-edit generated wrappers (edit SSOT instead)
- Run `sync_skills.py --check --target repo --publish-set repo_minimal` after changes

### Rollback Procedure

If scaffold fails or needs reverting:

1. Delete the SSOT skill directory:
   ```bash
   rm -rf /.system/skills/ssot/<group>/<skill-name>/
   ```
2. Regenerate wrappers:
   ```bash
   python scripts/devops/skills/sync_skills.py --regenerate --target repo --publish-set repo_minimal
   ```

---

## 7. Related Documents

- `/.system/skills/ssot/repo/skill-developer/skills-overview/SKILL.md` - Skill discovery overview
- `/.system/skills/ssot/repo/skill-developer/agent-skills-integration/SKILL.md` - Provider wrapper mapping
- `/.system/skills/ssot/repo/skill-developer/skill-development-patterns/SKILL.md` - Skill development patterns
- `/.system/skills/AGENTS.md` - Skills system strategy

---
> Source: [willyu1007/ai_first_template](https://github.com/willyu1007/ai_first_template) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-04 -->
