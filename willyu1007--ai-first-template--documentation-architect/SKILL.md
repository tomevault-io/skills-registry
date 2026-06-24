---
name: documentation-architect
description: Documentation architect workflow for structural changes. Keywords: documentation, architect, workflow. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Documentation Architect

This workflow creates or updates documentation with a strong emphasis on correct placement (strategy vs SSOT vs workdocs) and deterministic metadata.

---

## Purpose & Scope

Use this workflow when:
- You need to create/update durable SSOT docs under `/.system/skills/ssot/**`
- You need to improve documentation structure and discoverability
- You need to document a new feature, workflow, or integration

Out of scope:
- Writing project-specific operational runbooks inside SSOT (those belong in scenario-local workdocs or ops areas)

---

## Inputs & Preconditions

Inputs:
- What system/feature is being documented
- Target audience (AI systems vs humans)
- Required constraints (paths, forbidden references, metadata contract)

Preconditions:
- Read `/.system/skills/ssot/repo/documentation-conventions/documentation/conventions/overview.md`.
- Follow the most local applicable `AGENTS.md`.

---

## Steps

1. **Classify the doc type**
   - Strategy: `AGENTS.md`
   - SSOT: `/.system/skills/ssot/**` (skill packages)
   - Work-in-progress context: workdocs
2. **Gather authoritative context**
   - Read existing SSOT docs and relevant code/config (if applicable).
3. **Choose placement**
   - Prefer placing content under the owning skill package: `/.system/skills/ssot/<group>/<skill-name>/{SKILL.md,,examples/}`.
4. **Write with metadata**
   - For `SKILL.md`: ensure `name` and `description` meet the cross-provider constraints.
   - For supporting docs: no global front matter required.
5. **Cross-reference correctly**
   - Use repo-root absolute paths when referencing docs.
   - Avoid forbidden references from SSOT docs.
6. **Review for duplication**
   - Keep `AGENTS.md` short; link to SSOT instead of copying content.

---

## Outputs

- New/updated documentation files with correct metadata and links
- Updated navigation references (where appropriate)

---

## Safety Notes

- Do not embed secrets or environment-specific commands into long-term SSOT.
- Do not edit tool-owned marker blocks in ability catalogs or provider skill wrappers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
