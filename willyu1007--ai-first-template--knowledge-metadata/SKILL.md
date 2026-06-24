---
name: knowledge-metadata
description: YAML front matter contract for knowledge documents. Keywords: metadata, front matter, YAML. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Document Metadata (Skill Discovery SSOT)

This template treats skill packages under `/.system/skills/ssot/**` as the single SSOT for durable, AI-facing guidance.

Only SSOT skill entrypoints (`SKILL.md`) require a metadata contract; supporting docs do not.

---

## 1. Required metadata: `SKILL.md` front matter

Every SSOT skill entrypoint (`SKILL.md`) MUST include YAML front matter with:
- `name`: matches the skill directory name (kebab-case, <= 64 chars)
- `description`: single-line trigger description (<= 500 chars)

Optional:
- `metadata`: owner, module, domain, etc.
- `compatibility`: dependencies like git/docker/network.

### Example

```yaml
---
name: execution-plans
description: "Use when a task needs an execution-ready plan. Keywords: plan, checkpoints, gates."
metadata:
  owner: platform
---
```

---

## 2. Supporting docs: no global contract

Files under supporting files, `examples/`, `scripts/`, `templates/`, etc do not require YAML front matter. Keep them readable, stable, and linkable.

---

## 3. Workdocs metadata (separate)

Workdocs files (e.g., `workdocs/.../plan.md`) may start with a YAML header for tooling/checklists. This is independent from `SKILL.md` front matter.

---

## 4. Legacy knowledge directory contract (deprecated)

Older versions of this template used a knowledge directory with `id/doc_type/scope/summary` as a routable metadata contract. That system is deprecated and should be treated as archived history under `backup/` when present.

## 5. Related documents

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
